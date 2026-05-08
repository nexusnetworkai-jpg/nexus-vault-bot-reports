---
type: architecture
created: 2026-05-08
subject: Multi-Tenant SaaS Architecture Spec für ETH-Trading-Bot
participants: aaron, claude
status: approved-pending-implementation
phase: B (Multi-Tenant SaaS Foundation)
sub_phase: B2.1 (Spec)
implementation_phases: B2.2 - B2.8
estimated_effort_total: 4-6 Wochen für B2 komplett
---

# Multi-Tenant SaaS Architecture Spec

## Vision

Pure-SaaS-First Trading Platform für 200+ User. Live-Trading kommt zum Schluss nach voll-fertigem System. Master-Vault bleibt OPERATOR-ONLY (Aaron's strategic workshop). User sehen ALLES via Web-App-UI.

---

## Discovery — Was ist heute "global"?

### State-Files (`/app/logs/`)

| Datei | Inhalt | Multi-Tenant-Strategie |
|---|---|---|
| 53× `state_<PAIR>.json` | Position + paper_balance + paper_locked + daily_pnl + streaks | **per-user**: `logs/users/<id>/state_<pair>.json` |
| `runtime/circuit_breaker.json` | Circuit-state | per-user |
| `runtime/evolution.csv` | Evolution-tracking | per-user |
| `brain/memory.json` (51K) | stats + lessons + signal_scores + ml_accuracy | **shared anonymized aggregate** |
| `brain/experiences/` | Replay-buffer | shared anonymized |
| `brain/ml_model.pkl` | Trained model | shared (model = same for all) |
| `brain/allocator_state.json` | kill_switch + daily_pnl + strategy weights | **per-user** |
| `cache/binance_symbol_filters.json` | Binance LOT_SIZE/PRICE_FILTER per pair (24h TTL) | **stays global** (no PII, same for all) |

### Postgres-Tables (existing 24 tables)

**Need `tenant_id` column:**
```
trades, trades_all, positions_all, pnl_history,
bot_events, bot_config, agent_state, agents,
outcomes, pending_decisions, swarm_agents
```

**Stay global (shared learning/infra):**
```
brain_ml_model, brain_state, concepts, concept_links,
knowledge_items, build_queue
```

**Already user-aware (B1):**
```
users, user_settings, refresh_tokens, password_reset_tokens
```

### trades_hub.db (SQLite cache layer)
- Schema: id, symbol, side, entry_price, exit_price, qty, pnl_usd, opened_at, closed_at, status
- **Need tenant_id column + index** (mirror Postgres for fast read)

### Hot-Path (bot/engine.py)
- `_trade_pair(...)` (line 178) — pro pair eine iteration heute
- `_run_strategy_cycle(...)` (line 973) — S2/S5 strategies
- `run(config)` (line 1108) — main loop
- **Multi-tenant:** outer-loop über aktive User, pro user load context, run cycle, save state

### Brain (bot/brain.py + experience.py)
- `record_trade_result(pair, entry, exit, pnl, pnl_pct, signals_used, regime, hold_bars)` — fields sind anonymisierbar
- ⚠️ ABER: aktueller `stats.total_pnl` summiert alle pnls — würde alle user pnls mischen → BIAS
- → Brain darf Aggregate nur als **pattern-detection** nutzen, nicht als capital-tracking

---

## Architecture-Decisions

### Decision 1: Execution-Architektur

| Phase | Architektur | Wann |
|---|---|---|
| **A (MVP)** | **single-process user-loop** | jetzt, B2.7 |
| B | process-per-tenant | bei 30-50 aktiven Usern |
| C | job-queue (Celery/RQ) | Phase F+ (post 200 Usern) |

**A-Implementation:** main_v3.py iteriert über `users WHERE active=true AND credentials_valid=true`, ruft `_trade_pair_for_user(user_id, pair, ...)`. Shared market-data fetch (Cost-Optimization: 1× klines pro pair pro tick statt N×).

**Migration A→B:** wenn 30-50 Users gleichzeitig die loop-time > 30s machen, fork-pro-user-process. Existing code stays compatible (per-user-context-pattern macht's möglich).

### Decision 2: Brain-Sharing

**Brain = shared anonymized learning layer** (nicht per-user). Begründung: ein 200-User-Bot mit zentralem Brain lernt 200× schneller als 200 individual brains.

**Anonymized fields (shared in brain):**
- `pair_id` (z.B. ETHUSDT)
- `direction` (LONG/SHORT)
- `entry_indicators` (RSI, MACD, ADX, BB-position, ATR — Werte normalisiert)
- `exit_pnl_pct` (relative return, nicht USD)
- `regime` (trending/range/volatile)
- `duration_bars`
- `signals_used` (string-array)

**NEVER in brain:**
- `user_id`
- `capital_amount` / `notional_usd`
- `exact_timestamp_ms`
- `binance_account_id`

**Implementation:** `bot/brain.py:record_trade_result` Signature bleibt — caller muss Sicherheitsfilter setzen (user-id strip, capital strip, timestamp-bucket auf hour). Eigener PR in B2.7.

### Decision 3: User-Data-Surface (UPDATED per Aaron)

**KEINE per-user Vaults. KEINE per-user Repos. KEINE Git-Operationen pro User.**

| Wer | Sieht was wo |
|---|---|
| **Aaron (Operator)** | Master-Vault: Brain-State, Strategic Insights, Aggregat-Performance über alle User (anonym), eigene Decisions/Pre-Mortems |
| **User** | Web-App-UI exclusiv: Dashboard, Trade-History, Settings, Performance-Charts. **Kein Vault, kein git** |

**Master-Vault-Inhalte bleiben:**
- 02_Areas/Daily/ (1.7a daily-reports — aggregated über alle user, anonym)
- 02_Areas/Decisions/ (Aaron's strategic decisions)
- 02_Areas/Pre-Mortems/ (Aaron's risk planning)
- 02_Areas/Architecture/ (this doc + walkthroughs)
- 02_Areas/Procedures/ (Aaron's runbooks)
- 02_Areas/Proposals/ (Aaron's strategy-proposals via 1.7c)
- 02_Areas/session-context.md (1.7b — bot-overview für Aaron's CLI)

User-Data sit alle in **Postgres als single source of truth**, surfaced via API → React/Next.js Web-App.

### Decision 4: Per-User-Settings (extends B1 user_settings)

```sql
ALTER TABLE user_settings ADD COLUMN active_strategies JSON
    DEFAULT '["S4_MomentumV2"]';
ALTER TABLE user_settings ADD COLUMN paper_mode BOOLEAN
    DEFAULT TRUE;
ALTER TABLE user_settings ADD COLUMN stake_cap_usd FLOAT
    DEFAULT 1000.0;
ALTER TABLE user_settings ADD COLUMN entry_score_min FLOAT
    DEFAULT 0.15;
ALTER TABLE user_settings ADD COLUMN ml_threshold FLOAT
    DEFAULT 0.55;
ALTER TABLE user_settings ADD COLUMN tier VARCHAR(20)
    DEFAULT 'free';  -- free | pro | premium (controls pair-allocation)
```

Plus already-in-B1: `risk_per_trade`, `max_daily_loss_pct`, `max_drawdown_pct`, `pairs`.

### Decision 5: Per-User Binance-Keys (Option A — Envelope-Encryption)

```sql
CREATE TABLE user_credentials (
    user_id INTEGER PRIMARY KEY REFERENCES users(id) ON DELETE CASCADE,
    encrypted_dek BYTEA NOT NULL,           -- DEK encrypted with MASTER_KEK
    encrypted_binance_key BYTEA NOT NULL,   -- API-key encrypted with DEK
    encrypted_binance_secret BYTEA NOT NULL,-- secret encrypted with DEK
    binance_account_type VARCHAR(20) DEFAULT 'spot_margin',
    last_validated_at TIMESTAMP,
    permission_check JSON,                  -- {trading: true, withdrawal: false, ...}
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
CREATE INDEX idx_user_credentials_user ON user_credentials(user_id);
```

**Envelope-Encryption-Flow:**
1. Generate per-user DEK (32-byte random) on credential-create
2. Encrypt DEK with `MASTER_KEK` (Railway env-var, Fernet)
3. Encrypt user's Binance-API-Key + Secret with DEK (Fernet)
4. Store `encrypted_dek`, `encrypted_binance_key`, `encrypted_binance_secret`

**Order-Submit-Flow:**
1. Lazy-load user_credentials row
2. Decrypt DEK with MASTER_KEK
3. Decrypt Binance-key + secret with DEK
4. Make order via temp client
5. **Discard plaintext from memory** (overwrite + del)

**MASTER_KEK rotation policy:** envelope-pattern erlaubt re-encryption ALL DEKs ohne user-keys neu zu lesen. Quarterly rotation post-launch empfohlen.

**Onboarding-Wizard:**
1. Account create (B1 register)
2. Tutorial: "Wie generiere ich einen Binance-API-Key" (Step-by-Step mit Screenshots)
3. User pasted Binance Key + Secret in Form
4. **Validation-Test:** Bot macht 1× `client.get_account()` — verifiziert:
   - Authentication works
   - `canTrade=true`
   - `canWithdraw=false` ← BLOCKER wenn true (sicherheitsrisiko)
   - Margin-permission optional (für SHORT-trading)
5. Risk-Settings einstellen (default: 1% per trade)
6. Capital-Cap setzen (USD)
7. **PAPER_MODE=true initial** — User entscheidet wann live

### Decision 6: Anti-Self-Front-Running (5 Layers)

Wenn 200 User dasselbe Signal sehen und gleichzeitig dieselbe Order absenden, **front-running uns selbst** = wir sind der größte impact-creator im market. Mitigation in Layers:

#### Layer 1: Random Stagger 0-30s (in B2.7)
Pro user signal-trigger: `time.sleep(random.uniform(0, 30))` bevor order-submit.
**Effekt:** orders verteilen sich über 30s window.

#### Layer 2: Per-User Param-Tilt (in B2.8)
Pro user: kleines random offset auf entry-thresholds:
- RSI ±2 (z.B. user A entry at RSI 28, user B at RSI 30)
- ADX ±3
- MACD-threshold ±0.05

Diversifiziert entry-points naturally. Defaults aus user_settings hash(user_id, signal-name).

#### Layer 3: Pair-Allocation per Tier (in B2.8)
| Tier | Pairs |
|---|---|
| Free | BTCUSDT, ETHUSDT (2 highest-liquidity) |
| Pro | + SOL, ADA, AVAX, LINK, DOGE (mid-cap) |
| Premium | + ORCA, PEPE, SHIB, alle low-cap |

Diversifiziert pair-distribution naturally; Free-tier wettet nur auf high-liquidity wo our orders im noise verschwinden.

#### Layer 4: Capacity-Cap 200 + Waitlist (in B2.8)
Hard-limit `MAX_ACTIVE_USERS=200` env-var. Über 200: registration funktioniert, aber `active=false` mit waitlist-position. Email/in-app notification wenn slot frei.

#### Layer 5: Order-Aggregation (Phase F+, post-launch)
Statt 200 individuelle orders: 1 aggregierte order, post-fill internal allocation pro user-stake-share. Komplex — needs allocation-engine. Phase F+.

**B2-Scope:** Layers 1-4 implementieren, Layer 5 als Stub + Roadmap-Item dokumentieren.

### Decision 7: Database-Strategy

| Layer | Purpose |
|---|---|
| **Postgres** | Single source of truth: users, settings, credentials, trades, positions, pnl_history, brain-aggregate |
| **SQLite trades_hub.db** | Cache/staging-layer für fast-read, mit tenant_id columns + indexes; sync-loop schiebt von Postgres |
| **Local state-files** (`logs/users/<id>/`) | Per-user ephemeral runtime-state, recreated von Postgres beim Boot |

**Idempotent boot:** wenn state-file fehlt → restore aus Postgres-snapshot. Wenn beide stale → cold-start aus user_settings defaults.

### Decision 8: User-Data-API für Web-App

Alle hinter B1 Bearer-Auth, tenant-aware-Filtering im handler:

```
GET    /api/v3/me/portfolio       → balance, equity, total_pnl, daily_pnl
GET    /api/v3/me/trades?limit=50 → eigene trades, paginated
GET    /api/v3/me/positions       → open positions
GET    /api/v3/me/settings        → user_settings + tier
PUT    /api/v3/me/settings        → update settings (validated against tier)
POST   /api/v3/me/credentials     → save Binance keys (envelope-encrypt)
PUT    /api/v3/me/credentials     → update keys (re-encrypt)
DELETE /api/v3/me/credentials     → wipe keys, set active=false (Bot stops for user)
POST   /api/v3/me/paper-toggle    → switch paper/live (validates credentials when switching to live)
GET    /api/v3/me/onboarding-status → which wizard-step user is on
```

---

## Migration-Pfad B2.2 - B2.8

| Sub-Phase | Scope | Aufwand |
|---|---|---|
| **B2.2** | Schema-Migration (Postgres + SQLite tenant_id columns + indexes); Aaron's existing data → user_id=1 (Aaron als first user) | 3-5 Tage |
| **B2.3** | bot/user_context.py: get_user_context(user_id) → SettingsContext + StateContext; defaults aus user_settings, ENV als Fallback | 3-5 Tage |
| **B2.4** | Per-User-State-Refactor: BotState wird per-user, state-files unter logs/users/<id>/, Aaron's existing state migrated | 5-7 Tage |
| **B2.5** | user_credentials-Tabelle + bot/auth/credentials.py mit AES-256 envelope-encryption + Order-Submit-Pfade lazy-laden | 5-7 Tage |
| **B2.6** | Web-App User-Data-API (alle /api/v3/me/* endpoints + tenant-aware filtering + tests) | 3-5 Tage |
| **B2.7** | Engine User-Loop: main_v3.py iteriert über users, shared market-data fetch, Stagger Layer 1 | 7-10 Tage |
| **B2.8** | Anti-Front-Running Layers 2-4 (Param-Tilt + Pair-Allocation per Tier + Capacity-Cap) | 5-7 Tage |

**Total geschätzt: 4-6 Wochen** für B2 komplett (single-developer, full-time-equivalent).

### Reihenfolge-Begründung
- B2.2 zuerst (schema first — alles andere baut darauf)
- B2.3 vor B2.4 (settings sind read-only first; state-mutation kommt danach)
- B2.4 vor B2.5 (state-refactor ist invariant für credential-Pfad)
- B2.5 vor B2.6 (creds müssen funktional sein bevor /me/credentials endpoint live geht)
- B2.6 vor B2.7 (API testbar bevor engine sie consumt)
- B2.7 vor B2.8 (loop-architecture muss stehen bevor anti-frontrun layers ge-wired werden)

### Stop-or-Continue-Punkte zwischen Sub-Phasen
- **Nach B2.2:** Aaron's data successfully migrated zu user_id=1? Existing /api/v3/* unbroken? → continue oder rollback
- **Nach B2.4:** Aaron's bot läuft normal mit per-user-state? Paper-trades funktionieren? → continue
- **Nach B2.5:** envelope-encryption tested mit Aaron's keys re-encrypt + decrypt? → continue
- **Nach B2.7:** loop-time mit 1 user (Aaron) gleich oder besser als pre-refactor? → continue oder optimize

### Rollback-Strategien
- Schema-changes: alle ALTER TABLE ADD COLUMN sind backwards-compatible. Pre-deploy DB-snapshot via R2-backup-pipeline (existing).
- Code-changes: standard branch + revert. Falls schema bereits deployed: rollback-script `bot/migrations/<phase>_down.sql`.

---

## Risk-Assessment per Sub-Phase

| Sub-Phase | Risk-Level | Hauptrisiko | Mitigation |
|---|---|---|---|
| B2.2 | **niedrig** | tenant_id-DEFAULT für existing rows wrong | DEFAULT 1 für Aaron (er ist user_id=1) |
| B2.3 | niedrig | settings-default-conflicts mit ENV | klare Precedence-Order: user_settings > ENV > hardcoded |
| B2.4 | **mittel** | state-file-migration verliert Daten | dry-run + parallel-write (alt + neu) für 7 Tage |
| B2.5 | **HOCH** | encryption-key-loss = irrecoverable creds | MASTER_KEK rotation-fähig + env-var-backup-plan |
| B2.6 | niedrig | tenant-leak (user A sieht user B's trades) | source-grep guards in tests, rigorous tenant_id WHERE-clauses |
| B2.7 | **HOCH** | loop-time-explosion bei 30+ users | benchmarking pre-deploy, Migration-A→B-Trigger bei loop > 30s |
| B2.8 | mittel | tier-allocation rules zu rigid | start liberal, tighten basierend auf observed front-running |

---

## Dependencies + Pre-Requirements

| Dep | Status | Note |
|---|---|---|
| B1 Auth-System | ✓ live | tenant_id ready |
| Postgres (DATABASE_URL) | ✓ live | connection-pool ready |
| Backup-as-a-Service | ✓ live | pre-migration snapshots verfügbar |
| Vault-Pipeline | ✓ live | aggregated insights für Master-Vault |
| `MASTER_KEK` env-var | **OPEN** | Aaron muss vor B2.5 setzen |
| Tier-pricing-decision | OPEN | Aaron decision für free/pro/premium boundaries |

---

## Worth Noting

- **Brain-Sharing schafft Network-Effects:** je mehr User, desto schneller lernt das System. Pricing-Hebel: Premium-Users tragen disproportional zum Lernen bei (mehr Pairs, mehr Trades) → premium-features-Quid-pro-Quo.
- **Anonymization is the moat:** wenn ein User cancelt, brain-knowledge bleibt. Wenn ein Competitor das Brain stehlen würde, bekäme er trade-patterns aber keine identifying-info → DSGVO-konform und IP-geschützt.
- **Capacity-Cap 200 ist Decision Layer 4 PLUS Pricing-Lever:** waitlist creates urgency, premium-tier könnte queue-jump erlauben.
- **Master-Vault als IP-Moat:** Aaron's Strategy-Decisions, Pre-Mortems, Brain-Insights = Aaron's Operator-Intelligence. NIE direkt user-exposed → eigene strategische Differenzierung.
- **Live-Trading kommt zum Schluss:** komplettes SaaS-System läuft erst paper-only mit allen 200 user, dann Live-Cutover-Flag (per-user, individually) basierend auf Aaron's Stake-Cap-Decision-Pattern.

_Tags: #architecture #multi-tenant #b2-spec #saas-foundation #2026-05-08_
