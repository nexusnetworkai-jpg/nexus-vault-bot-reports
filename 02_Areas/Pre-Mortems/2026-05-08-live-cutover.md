---
type: pre-mortem
created: 2026-05-08
subject: Live-Cutover (PAPER_MODE=false) für ETH-Trading-Bot
participants: aaron, claude
status: approved-pending-cutover
---

# Pre-Mortem — Live-Cutover (PAPER_MODE=false)

## Was wir vorhaben

Switch von paper-mode auf live-trading mit echtem Kapital auf Binance.
Initial-Capital wird in folgender Stake-Cap/Kelly-Sitzung entschieden.
Aktive Strategien: S2_StatArb, S4_MomentumV2, S5_LiqHunter.
Direction-balanced (verifiziert post-B-S1: 689 SHORT / 874 LONG
trades lifetime, beide Direction profitabel, SHORT avg-win höher
$12.41 vs LONG $9.74).

## Failure-Modes (9 identifiziert)

### 1. Binance LOT_SIZE/PRICE_FILTER-Reject

Pre-§B-H5 hatte executor.py `round(qty, 5)` und `round(price, 2)`
hardcoded für ALLE pairs. Live wäre nur BTC/ETH zuverlässig — DOGE,
SHIB, PEPE, PENGU, ORCA, PUMP hätten mit "Filter failure: LOT_SIZE"
oder "PRICE_FILTER" gerejected.

**Status:** GEFIXT in §B-H5 (`bot/binance_filters.py`). Per-symbol
exchange_info-cache mit 24h TTL, 6 round-sites in executor refactored.
Pre-order rounding + validate_order-Gate.

**Detection live:** order-rejection-counts in /api/v3/status sollten
~0 bleiben. Falls > 0: Binance-filter-cache reload triggern.

### 2. Position-Mismatch Bot ↔ Binance

Bot-state.position differs from Binance-actual position (durch ghost-
position pre-§B-K6, oder reconcile-failure, oder partial-fill nicht
korrekt erfasst).

**Status:** §B-K6 fixed sell-before-state-clear. §B-H2 fixed
paper_locked-drift nach PARTIAL.

**Detection live:** reconcile_positions-loop läuft jeden cycle. Falls
mismatch detected: emergency_close_all + alert + bot_pause.

**Mitigation:** vor erstem cutover-trade: position-audit gegen
Binance-account, alle state_*.json gegen Binance-balances cross-check.

### 3. Binance API Rate-Limits / 429s

Binance limits: 1200 weight/min, 50 orders/10s. Bei multi-pair (12
active pairs) + swarm-vote-loop könnten wir das überschreiten.

**Status:** kein expliziter rate-limiter im executor — verlässt sich
auf python-binance's eingebaute backoff. Bei 429: bot retried.

**Detection live:** logs nach `429 Too Many Requests` oder
`-1003 Way too many requests`.

**Mitigation:** falls 429-spam: max_concurrent_orders config-knob,
oder weniger active pairs.

### 4. Drawdown > 10% in den ersten 24h

Live-Volatility kann höher sein als paper, slippage real, fees real.
Bot's adaptive sizing kann unerwartet aggressiv werden bei early
losses (kelly criterion).

**Status:** §H-4 wirelt allocator.update_pnl in engine. Allocator's
kill-switch feuert bei daily_loss > 3% oder drawdown > 15% von peak.
§B-H2 fix verhindert paper_locked-drift die adaptive sizing
miscalibrate würde.

**Zusätzlich:** SHORT-Trades involvieren `margin_executor`, der nie
live-getestet ist. Erste Live-SHORT-Order ist High-Risk-Moment —
extra Aufmerksamkeit auf den ersten margin-call/liquidation-Path.

**Detection live:** /api/v3/allocator zeigt kill_switch=true wenn
threshold reached. Telegram-alert auf "ALLOCATOR KILL SWITCH" log-line.

**Stop:** > 5% drawdown in 1h → manueller stop. > 10% drawdown in
24h → revert zu paper, root-cause-analysis.

### 5. Paper-vs-Live Divergenz

Paper-mode simuliert fills @ market price ohne slippage, ohne
partial-fills, ohne maker/taker-fees. Live: alles davon.

**Status:** kein expliziter slippage-modell in paper-mode (kosmetisches
follow-up wäre paper-mode mit slippage-simulation).

**Detection live:** vergleich realisierte exit_price vs intended exit
in trades_hub.db nach 24h. Wenn average-slippage > 0.1%: feed-Probleme
oder zu aggressiver market-order-style.

**Mitigation:** falls slippage hoch: switch zu LIMIT-orders mit
small spread, oder reduzierte trade-frequency.

### 6. Telegram-Outage

Telegram-API down → bot operiert weiter blind, Aaron bekommt keine
Alerts. Worst case: drawdown läuft 12h ohne dass es jemand merkt.

**Status:** §Stage1-cleanup task 3 verifiziert telegram roundtrip
end-to-end (3/3 messages received). Code retried nicht bei API-fail —
silent skip.

**Detection:** Telegram-bot-status manueller check. Plus: secondary
channel = R2 backups + session-context.md updates (Aaron's CLI
schaut auf Vault-state).

**Mitigation:** falls telegram down länger: Aaron checkt /api/v3/
status manuell oder via session-context refresh.

### 7. Bot-Crash mit offener Position

Container crash, Railway-restart, OOM, etc. State-files persistieren
auf volume, sollten beim restart-restore intakt sein.

**Status:** state-files via `state.save(f"logs/state_{pair}.json")`
nach jedem critical event. Boot-time `_load` aus state-file. Plus:
backup-as-a-service (R2) hat daily-snapshot.

**Detection:** main_v3.py boot-logs zeigen "loaded state for X pairs".

**Mitigation:** wenn crash UND state corrupted: restore aus latest
R2-backup. Restore-doc steht in audit/backups/RESTORE.md (out-of-
this-pre-mortem todo).

### 8. Critical Bug not in audit (KONKRET-Beispiel B-S1)

**Vorgeschichte:** Im April-Audit wurde sync_loop SHORT-Filter
übersehen. 14 Tage Paper-Soak liefen mit DB-Aggregations, die nur
50% der Trades sahen. Brain-regime-patterns + Experience-Replay
waren long-biased ohne dass es jemand bemerkt hat. Erst durch
manuelles Audit (audit/short-capability-audit.md) Anfang Mai entdeckt.

**Lehre:** Pre-Cutover-Sanity-Checks mit folgender Disziplin:
- Direction-Split jeder DB-Query als smoke-test (LONG-count ==
  SHORT-count grobe Größenordnung match)
- Konsistenz-Checks zwischen CSV und DB (count-vergleich nach
  jedem deployment das sync_loop oder data-pipeline berührt)
- Result-Verdicts immer mit "n=X (split N long / M short)"
  qualifizieren, nicht bare totals

**Detection künftig:** session-context daily report sollte
direction-split nebeneinander zeigen (eigene Sub-Phase post-cutover).

### 9. DB-Aggregations-Drift (NEU, Post-B-S1-Lehre)

**Risiko:** wenn künftig eine neue trade-action eingeführt wird (z.B.
"REBALANCE", "LIQUIDATION", "FEE_ADJUSTMENT") und sync_loop sie nicht
explizit handhabt, drifted DB stillschweigend von CSV. Gleiche
Klasse von Bug wie B-S1, nur mit anderer action.

**Detection:** weekly automated reconciliation: count(trades.csv) vs
count(trades_hub.db) — alarm wenn Differenz > 5%. Implementierbar
als neuer admin-endpoint `/api/v3/admin/sync/reconcile` (post-cutover).

**Prevention:** sync_loop-Erweiterung bei jedem neuen action-type ist
ab jetzt explizit dokumentationspflichtig in commit-msg. PR-checkliste:
"Wird neue action in CSV gelogged? → sync_loop._pair_trades-handler
erweitert? → side-attribute korrekt?"

**Mitigation:** backfill-Skript-Pattern (analog
`scripts/backfill_shorts_to_hub_db.py`) für historische Daten. Jede
neue action sollte ein eigenes backfill-Skript liefern.

## Stop-Trigger

1. Drawdown > 5% in der ersten Stunde → STOP, manueller review
2. Drawdown > 10% in 24h → REVERT zu paper, root-cause-analysis
3. > 5 Binance-API-errors in 1 Minute (kein 429-backoff-recovery) → STOP
4. Position-Mismatch Bot↔Binance detected → emergency_close_all + STOP
5. Telegram-outage > 30min UND keine sekundäre Aufsicht → STOP
6. Allocator kill_switch=true UND nicht offensichtliche Erklärung → STOP
7. Brain-confidence-collapse (lifetime win-rate fällt > 10% in 24h) → STOP
8. CSV-vs-DB-Reconciliation zeigt Differenz > 10% → ABBRECHEN,
   sync-loop-Audit
9. Erste 10 Live-SHORTs zeigen unerwartetes Verhalten (margin-call,
   liquidation, falsche PnL) → SHORT-Strategien deaktivieren
   temporär, weiter Long-only

## Pre-Cutover-Checkliste (alle GREEN müssen sein)

- [x] Pre-Mortem committed (this doc)
- [x] B-S1 sync_loop SHORT-fix landed + backfill done
- [x] Re-Audit confirms Brain not catastrophically biased
- [x] All test suites green (282/282 + 53 pre-existing ModuleNotFound)
- [x] paper_balance == starting_balance + total_pnl exakt (J-1)
- [x] Backup-as-a-Service verified (R2 daily backups laufen)
- [x] Telegram-Roundtrip verified (3/3 received)
- [ ] Stake-Cap und Kelly-Tuning explizit entschieden
- [ ] margin_executor Binance-Testnet-Verifikation
- [ ] Position-Audit complete (state.position vs Binance-actual)
- [ ] Aaron-mental-state: nicht müde, nicht gestresst, nicht im FOMO

## Watch-Window post-Cutover (erste 48h)

- **Erste 1h:** ich sitze davor, alle 5min /api/v3/status check, jeder
  trade manuell nachvollziehbar
- **Erste 6h:** alle 30min check, jede Telegram-Notification gegen-
  prüfen, drawdown beobachten
- **Erste 24h:** stündlicher check, daily-pnl tracken, allocator-state
- **Spezifisch erste 24h:** ersten LIVE-SHORT-Trade pro Pair manuell
  verifizieren — Margin-Account-State, korrekte Position-Direction,
  plausibel PnL bei Close. SHORT-Code-Pfad (margin_executor) war
  pre-cutover NIE live exercised
- **24-48h:** zurück zu Paper-Mode-Style-Beobachtung (4× pro Tag),
  aber daily-report-review täglich morgens
- **ORCAUSDT-Performance separat tracken** (lifetime hidden gem
  $194/trade avg, post-B-S1 erst sichtbar) — Decision-Journal-Eintrag
  bei Auffälligkeiten

## Sign-off

Aaron + Claude haben dieses Pre-Mortem gelesen, alle 9 Failure-Modes
verstanden, Stop-Trigger akzeptiert. Cutover wird erst nach Vervoll-
ständigung der offenen Checklist-Items (Stake-Cap, margin-Testnet,
Position-Audit, Aaron-Mentalstate) angefasst.

_Tags: #pre-mortem #live-cutover #2026-05-08 #B-S1-lessons_
