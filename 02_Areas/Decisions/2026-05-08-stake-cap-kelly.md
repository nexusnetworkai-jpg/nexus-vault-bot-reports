---
type: decision
created: 2026-05-08
subject: Live-Cutover Stake-Cap & Risk-Parameters für Aaron
participants: aaron, claude
status: approved-pending-cutover
applies_to_user: aaron (single-user phase)
post_multi_tenant_note: "Werte werden in Phase B zu user_settings-defaults für neue User; bestehende User behalten individuelle Settings"
---

# Decision — Live-Cutover Stake-Cap & Risk-Parameters

## Decision Summary

Initial Live-Capital: **€1,000** (konservativer Start, am Floor der
Strategy-Viability — fees+slippage frieren etwas Edge ein, aber
viable für 7-Tage-Validierung).

## Risk-Parameter (Phase A: ENV-Var-basiert, Phase B: per-User-DB-basiert)

| Parameter | Wert | Effektiv | ENV-Var-Name |
|---|---|---|---|
| Risk per trade | 1.0% | €10 max | `RISK_PER_TRADE=0.01` |
| Daily loss limit | 3.0% | €30 → cooldown | `MAX_DAILY_LOSS_PCT=3.0` |
| Max drawdown stop | 10.0% | €100 → emergency_stop | `MAX_DRAWDOWN_PCT=10.0` |
| Stake cap | €1,000 | Initial pool | `LIVE_CAPITAL=1000` (or PAPER_BASE_USDT-Umrechnung) |

## Pair-Staffelung

```
Tag 1-7:   3 Pairs   → ETHUSDT, BTCUSDT, ORCAUSDT
Tag 8-14:  6 Pairs   → +SOLUSDT, DOGEUSDT, LINKUSDT
Tag 15-30: 12 Pairs  → alle aktiven, schrittweise via Vault-Proposals
```

ENV: `PAIRS="ETHUSDT,BTCUSDT,ORCAUSDT"` (initial)

ORCAUSDT ist explizit in der Initial-3 weil post-B-S1-Re-Audit
zeigt: lifetime hidden gem ($194/trade avg, top-1 lifetime PnL
$3,306 aus 17 SHORT-trades). Verdient eigene Live-Beobachtung von
Tag 1.

## Strategy-Allocation (unverändert von Paper)

- S2_StatArb: **25%**
- S4_MomentumV2: **50%**
- S5_LiqHunter: **15%**
- S3_MarketMaking: **0%** (deaktiviert, planned)

Allocator-Werte aus existing config, nicht ändern am Cutover-Tag.
Re-Calibration erst nach 14 Tagen Live-Daten via separate Decision.

## SHORT-Behandlung

SHORT enabled wie in Paper — ABER:
- `margin_executor` MUSS pre-cutover Binance-Testnet-verifiziert sein
- Erste echte LIVE-SHORT pro Pair wird manuell verifiziert (Watch-Window)
- Bei unerwartetem SHORT-Verhalten: Stop-Trigger #9 aus Pre-Mortem
  (SHORT-Strategien deaktivieren temporär, weiter Long-only)

Re-Audit-Datenbasis (post-B-S1):
- SHORTs sind 50% des Lifetime-PnL ($8,548 vs LONG $8,509)
- Höherer avg-win pro SHORT: **$12.41** vs LONG **$9.74**
- Niedrigere SHORT-win-rate (54.3% vs 62.2%) — selektiver, aber
  größere wins
- Direction-Diversifikation ist real → SHORT abschalten würde 50%
  des erwarteten PnL eliminieren

## Kelly-Math-Begründung

Theoretisches Full-Kelly bei combined ~58% win-rate + ~1.5:1
win/loss-ratio: ~25-30% per trade. Praktisch tödlich (Drawdowns > 50%).

Industry-Standard: 1/4 bis 1/10 Kelly. Wir starten bei **~1/25**
(1.0% per trade) weil:

- Erste 30 Tage sind Kalibrierung, nicht Maximierung
- Slippage + Live-Spreads erodieren Edge unbekannt-stark
- p (win-rate) und b (win/loss-ratio) aus Paper sind nicht
  garantiert identisch in Live
- Erste Live-SHORTs sind margin_executor-Premiere → höheres
  unbekanntes Risiko

**Skalierung-Plan:**
- Tage 1-30: **1.0%** per trade (current)
- Tage 31-90: bei sauberen Daten → **1.5%**
- Tage 91+: bei sustained edge → **2.0%** (= ~1/12 Kelly)
- NIE über 2.5% ohne separate Decision-Journal-Eintrag

Trigger für Up-Step:
- 30d/60d ohne Stop-Trigger ausgelöst
- Live win-rate ≥ 55% (matches paper-baseline within tolerance)
- Drawdown peak < 8% in der vergangenen Periode
- Aaron-mental-state stabil (kein FOMO-Druck zur Erhöhung)

## Watch-Disziplin erste 48h (aus Pre-Mortem)

- Stündlich /api/v3/status + Telegram-scan (während wach)
- Erste Trade pro Pair: Console-Log Filter-Error-Check (LOT_SIZE,
  PRICE_FILTER post-§B-H5)
- Erste LIVE-SHORT pro Pair: Margin-Account-State manuell verifizieren
  (margin_executor-Premiere)
- Erste BE-/Trail-Exit: Latch-Verhalten validieren (post-§J-2/J-4)
- Daily Vault-Report lesen (1.7a-Pipeline)
- session-context.md morgens als CLI-context-load

## Mental-State-Check

Aaron-Bauchgefühl beim Cutover-Tag dokumentieren als go/no-go-Sanity-
Check. Wenn unruhig: **3 Tage warten ist billiger als ein vermeidbarer
Fehler unter Hetze.**

Spezifische Indikatoren für "warten":
- Schlafmangel (< 6h vorherige Nacht)
- Konflikte/stress-Quellen außerhalb Trading
- Unklare Erwartungshaltung ("könnte explodieren") statt nüchterne
  Validierungs-Mindset
- Druck-Gefühl von außen ("muss endlich live")

## Architektur-Note für SaaS-Vision

Diese Werte sind in Phase A (single-user) als ENV-Vars in Railway
gesetzt. In Phase B (multi-tenant-refactor) werden sie zu
`user_settings`-Records, jeder User mit eigenen Werten. Code-Änderung
minimal:

```python
# Phase A:
risk_pct = float(os.getenv("RISK_PER_TRADE", "0.01"))

# Phase B:
risk_pct = user_settings.get(user_id, "risk_per_trade", default=0.01)
```

Default-Fallbacks für neue User: konservative Werte aus diesem Doc.

**KEIN Wert ist im Bot-Code hardcoded** — alles via ENV → später via DB.
Diese Disziplin ist wichtig: ein hardcoded Wert würde Phase B zu
einer code-Migration statt zu einer config-Migration machen.

## Pre-Cutover-Checkliste (Update zum Pre-Mortem)

| Item | Status |
|---|---|
| Pre-Mortem committed | ✓ (`02_Areas/Pre-Mortems/2026-05-08-live-cutover.md`) |
| **Stake-Cap & Kelly entschieden** | ✓ (this doc) |
| margin_executor Binance-Testnet-Verifikation | OPEN |
| Position-Audit (state.position vs Binance-actual) | OPEN |
| `LIVE_CAPITAL`, `PAIRS`, `RISK_PER_TRADE` in Railway env | OPEN |
| Aaron-mental-state am Cutover-Tag dokumentieren | live-Schritt |
| `PAPER_MODE=false` setzen + Bot-Restart | live-Schritt |

## Sign-off

Aaron + Claude: gelesen, verstanden, approved. Cutover frühestens nach:
1. margin_executor-Testnet-Run grün
2. Position-Audit clean
3. Aaron-mental-state-Check pass

_Tags: #decision #live-cutover #stake-cap #kelly #2026-05-08_
