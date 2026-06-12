---
generated_at: 2026-06-12T00:01:04+00:00
generated_by: ethbot
version: 31b7382
freshness_minutes: 0
type: session-context
tags: [session-context, bot-snapshot]
---

# Session Context — 2026-06-12

> **Memory-Rule für Claude CLI:** Lies diese Datei IMMER als erste
> Aktion einer Session. Sie enthält den aktuellen Lage-Bild des
> ETH-Trading-Bots und der laufenden Vault-Pipelines.

## 🎯 Bot State (live snapshot)

- paper_mode: **False**
- version: `31b7382`
- paper_balance: **$100,000.00** (= starting $100,000 + total_pnl $+0.00)
- daily_pnl: **$+0.00**
- today_trades: 0
- lifetime win-rate: 51.7%
- open_positions: 0 pair(s)
- paper_locked: $0.00
- streaks: win=0 / loss=0

## 📈 7-Day PnL Trajectory

| Day | trades | pnl | wins | losses | win_rate |
|---|---|---|---|---|---|
| 2026-06-05 | 10 | $-919.07 | 0 | 10 | 0% |
| 2026-06-06 | 6 | $-3,312.76 | 0 | 6 | 0% |
| 2026-06-07 | 9 | $-4,293.60 | 0 | 9 | 0% |
| 2026-06-08 | 22 | $-9,273.40 | 9 | 13 | 41% |
| 2026-06-09 | 130 | $-183.23 | 78 | 52 | 60% |
| 2026-06-10 | 36 | $-3,829.83 | 6 | 30 | 17% |
| 2026-06-11 | 60 | $-3,163.30 | 17 | 43 | 28% |

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

- ⚠️ **WIN_RATE_DROP** · `XRPUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `XRPUSDT` — 6 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `AVAXUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `AVAXUSDT` — 5 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `LINKUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `BTCUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `BTCUSDT` — 17 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `SOLUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `SOLUSDT` — 24 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `DOGEUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `DOGEUSDT` — 17 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `ETHUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `TONUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `ADAUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `ADAUSDT` — 6 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `PLUMEUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `TRXUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `TRXUSDT` — 25 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `VANAUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `NILUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `SPKUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `SPKUSDT` — 6 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `NEARUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `NEARUSDT` — 7 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `WLDUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `WLDUSDT` — 10 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `ALLOUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `OPGUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `OPGUSDT` — 6 consecutive losing SELLs

## 🛠 Watchlist Suggestions

- Schau dir `XRPUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `XRPUSDT` mit 6 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `AVAXUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `AVAXUSDT` mit 5 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `LINKUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `BTCUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `BTCUSDT` mit 17 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `SOLUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `SOLUSDT` mit 24 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `DOGEUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `DOGEUSDT` mit 17 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `ETHUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `TONUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `ADAUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `ADAUSDT` mit 6 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `PLUMEUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `TRXUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `TRXUSDT` mit 25 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `VANAUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `NILUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `SPKUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `SPKUSDT` mit 6 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `NEARUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `NEARUSDT` mit 7 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `WLDUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `WLDUSDT` mit 10 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `ALLOUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `OPGUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `OPGUSDT` mit 6 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.

## 📜 Recent Commits (last 7)

_(git log unavailable)_

## 🚧 Open Sub-Phases (Watch-Files)

- 🚧 `audit/api-version-field-watch.md` (in-progress)
- 🚧 `audit/b1-auth-reactivation-watch.md` (in-progress)
- 🚧 `audit/b2-1-multi-tenant-architecture-spec-watch.md` (in-progress)
- 🚧 `audit/b2-2-schema-migration-watch.md` (in-progress)
- 🚧 `audit/b2-3-user-context-watch.md` (in-progress)
- 🚧 `audit/b2-4-state-refactor-watch.md` (in-progress)
- 🚧 `audit/b2-5-credentials-encryption-watch.md` (in-progress)
- 🚧 `audit/b2-6-me-endpoints-watch.md` (in-progress)
- 🚧 `audit/b2-7-engine-user-loop-watch.md` (in-progress)
- 🚧 `audit/b2-7b-1-2-remaining-guards-watch.md` (in-progress)
- 🚧 `audit/b2-7b-1-3-compute-decision-wiring-watch.md` (in-progress)
- 🚧 `audit/b2-7b-2-trade-pair-user-aware-watch.md` (in-progress)
- 🚧 `audit/b2-7b-3-flag-flip-cutover-watch.md` (in-progress)
- 🚧 `audit/b2-7b-3a-list-active-users-fix-watch.md` (in-progress)
- 🚧 `audit/b2-7b-3b-multi-user-diagnostics-watch.md` (in-progress)
- 🚧 `audit/b2-7b-3c-cutover-watch.md` (in-progress)
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
- 🚧 `audit/margin-executor-verification-watch.md` (in-progress)
- 🚧 `audit/post-bs1-re-audit-watch.md` (in-progress)
- 🚧 `audit/pre-cutover-final-prep-watch.md` (in-progress)
- 🚧 `audit/stage1-7a-vault-pipeline-watch.md` (in-progress)
- ✓ `audit/stage1-7b-auto-context-watch.md` (complete)
- 🚧 `audit/stage1-7c-vault-proposals-watch.md` (in-progress)
- 🚧 `audit/stage1-7d-decision-journal-watch.md` (in-progress)
- 🚧 `audit/stage1-backup-service-watch.md` (in-progress)
- 🚧 `audit/stage1-ops-cleanup-watch.md` (in-progress)

## 📍 Where We Left Off

Open anomaly demanding attention: **WIN_RATE_DROP** on `XRPUSDT`
