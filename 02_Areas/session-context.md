---
generated_at: 2026-05-15T00:01:04+00:00
generated_by: ethbot
version: 7e5f111
freshness_minutes: 0
type: session-context
tags: [session-context, bot-snapshot]
---

# Session Context — 2026-05-15

> **Memory-Rule für Claude CLI:** Lies diese Datei IMMER als erste
> Aktion einer Session. Sie enthält den aktuellen Lage-Bild des
> ETH-Trading-Bots und der laufenden Vault-Pipelines.

## 🎯 Bot State (live snapshot)

- paper_mode: **False**
- version: `7e5f111`
- paper_balance: **$126,187.24** (= starting $100,000 + total_pnl $+26,187.24)
- daily_pnl: **$+0.00**
- today_trades: 0
- lifetime trades: 5926
- lifetime win-rate: 52.6%
- open_positions: 0 pair(s)
- paper_locked: $0.00
- streaks: win=0 / loss=0

## 📈 7-Day PnL Trajectory

| Day | trades | pnl | wins | losses | win_rate |
|---|---|---|---|---|---|
| 2026-05-08 | 187 | $+2,586.78 | 115 | 71 | 62% |
| 2026-05-09 | 132 | $+647.63 | 66 | 64 | 51% |
| 2026-05-10 | 185 | $+4,277.68 | 131 | 52 | 72% |
| 2026-05-11 | 135 | $+1,136.89 | 70 | 63 | 53% |
| 2026-05-12 | 152 | $+675.15 | 78 | 70 | 53% |
| 2026-05-13 | 115 | $-11,096.46 | 55 | 58 | 49% |
| 2026-05-14 | 21 | $-983.66 | 9 | 12 | 43% |

## 🧠 Active Strategy Proposals

### Drafts (Aaron noch dran) (0)

_(keine)_

### Active (warten auf next trigger) (0)

_(keine)_

### Recently Applied (1)

- [✓] E2E Test — TP_MAX Round-Trip `TP_MAX` 0.025 → 0.030 _2026-05-02T11:19:33+00:00_ · `02_Areas/Proposals/2026-05-02-e2e-test-tp_max.md`

### Recently Rejected (0)

_(keine)_

## 🔔 Open Anomalies (heutige Daily-Note)

- ⚠️ **WIN_RATE_DROP** · `XRPUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `SOLUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `SOLUSDT` — 7 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `DOGEUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `ETHUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `SUIUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `SUIUSDT` — 5 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `PLUMEUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `ZECUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `ZECUSDT` — 9 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `VANAUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `NILUSDT` — last-5 win-rate 20% (< 30%)

## 🛠 Watchlist Suggestions

- Schau dir `XRPUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `SOLUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `SOLUSDT` mit 7 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `DOGEUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `ETHUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `SUIUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `SUIUSDT` mit 5 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `PLUMEUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `ZECUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `ZECUSDT` mit 9 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `VANAUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `NILUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?

## 📜 Recent Commits (last 7)

_(git log unavailable)_

## 🚧 Open Sub-Phases (Watch-Files)

- 🚧 `audit/api-version-field-watch.md` (in-progress)
- 🚧 `audit/b1-auth-reactivation-watch.md` (in-progress)
- 🚧 `audit/b2-2-schema-migration-watch.md` (in-progress)
- 🚧 `audit/b2-3-user-context-watch.md` (in-progress)
- 🚧 `audit/b2-4-state-refactor-watch.md` (in-progress)
- 🚧 `audit/b2-5-credentials-encryption-watch.md` (in-progress)
- 🚧 `audit/b2-6-me-endpoints-watch.md` (in-progress)
- 🚧 `audit/b2-7-engine-user-loop-watch.md` (in-progress)
- 🚧 `audit/b2-7b-1-2-remaining-guards-watch.md` (in-progress)
- 🚧 `audit/b2-7b-2-trade-pair-user-aware-watch.md` (in-progress)
- 🚧 `audit/b2-7b-3a-list-active-users-fix-watch.md` (in-progress)
- 🚧 `audit/b2-7b-3b-multi-user-diagnostics-watch.md` (in-progress)
- 🚧 `audit/b2-7b-engine-body-refactor-watch.md` (in-progress)
- 🚧 `audit/b2-7c-paper-locked-recompute-watch.md` (in-progress)
- 🚧 `audit/b2-7d-position-sizer-cap-watch.md` (in-progress)
- 🚧 `audit/bs2-short-close-fix-watch.md` (in-progress)
- 🚧 `audit/bs3-partial-sell-invariant-watch.md` (in-progress)
- 🚧 `audit/h4-allocator-scale-watch.md` (in-progress)
- ✓ `audit/h5-lot-size-watch.md` (complete)
- 🚧 `audit/j1-paper-balance-sot-watch.md` (in-progress)
- 🚧 `audit/j2-j4-exit-logic-watch.md` (in-progress)
- 🚧 `audit/j3-macd-confidence-watch.md` (in-progress)
- 🚧 `audit/pre-cutover-final-prep-watch.md` (in-progress)
- 🚧 `audit/stage1-7a-vault-pipeline-watch.md` (in-progress)
- ✓ `audit/stage1-7b-auto-context-watch.md` (complete)
- 🚧 `audit/stage1-7c-vault-proposals-watch.md` (in-progress)
- 🚧 `audit/stage1-7d-decision-journal-watch.md` (in-progress)
- 🚧 `audit/stage1-backup-service-watch.md` (in-progress)
- 🚧 `audit/stage1-ops-cleanup-watch.md` (in-progress)

## 📍 Where We Left Off

Open anomaly demanding attention: **WIN_RATE_DROP** on `XRPUSDT`
