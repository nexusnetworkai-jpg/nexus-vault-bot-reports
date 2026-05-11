---
generated_at: 2026-05-11T00:01:04+00:00
generated_by: ethbot
version: b9ce914
freshness_minutes: 0
type: session-context
tags: [session-context, bot-snapshot]
---

# Session Context — 2026-05-11

> **Memory-Rule für Claude CLI:** Lies diese Datei IMMER als erste
> Aktion einer Session. Sie enthält den aktuellen Lage-Bild des
> ETH-Trading-Bots und der laufenden Vault-Pipelines.

## 🎯 Bot State (live snapshot)

- paper_mode: **True**
- version: `b9ce914`
- paper_balance: **$146,723.45** (= starting $100,000 + total_pnl $+46,723.45)
- daily_pnl: **$+0.00**
- today_trades: 0
- lifetime trades: 4788
- lifetime win-rate: 53.7%
- open_positions: 0 pair(s)
- paper_locked: $0.00
- streaks: win=0 / loss=0

## 📈 7-Day PnL Trajectory

| Day | trades | pnl | wins | losses | win_rate |
|---|---|---|---|---|---|
| 2026-05-04 | 168 | $+2,348.92 | 106 | 61 | 63% |
| 2026-05-05 | 177 | $+2,516.84 | 107 | 69 | 61% |
| 2026-05-06 | 187 | $-1,187.31 | 106 | 79 | 57% |
| 2026-05-07 | 225 | $+4,400.20 | 135 | 85 | 61% |
| 2026-05-08 | 187 | $+2,586.78 | 115 | 71 | 62% |
| 2026-05-09 | 132 | $+647.63 | 66 | 64 | 51% |
| 2026-05-10 | 185 | $+4,277.68 | 131 | 52 | 72% |

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

- ⚠️ **WIN_RATE_DROP** · `PLUMEUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `VANAUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `NILUSDT` — last-5 win-rate 20% (< 30%)

## 🛠 Watchlist Suggestions

- Schau dir `PLUMEUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
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

Open anomaly demanding attention: **WIN_RATE_DROP** on `PLUMEUSDT`
