---
generated_at: 2026-05-08T00:01:04+00:00
generated_by: ethbot
version: e19c312
freshness_minutes: 0
type: session-context
tags: [session-context, bot-snapshot]
---

# Session Context — 2026-05-08

> **Memory-Rule für Claude CLI:** Lies diese Datei IMMER als erste
> Aktion einer Session. Sie enthält den aktuellen Lage-Bild des
> ETH-Trading-Bots und der laufenden Vault-Pipelines.

## 🎯 Bot State (live snapshot)

- paper_mode: **True**
- version: `e19c312`
- paper_balance: **$123,172.30** (= starting $100,000 + total_pnl $+23,172.30)
- daily_pnl: **$+0.00**
- today_trades: 0
- lifetime trades: 2722
- lifetime win-rate: 53.3%
- open_positions: 0 pair(s)
- paper_locked: $75,694.39
- streaks: win=0 / loss=0

## 📈 7-Day PnL Trajectory

| Day | trades | pnl | wins | losses | win_rate |
|---|---|---|---|---|---|
| 2026-05-01 | 51 | $+222.59 | 35 | 15 | 70% |
| 2026-05-02 | 50 | $+1,944.67 | 29 | 20 | 59% |
| 2026-05-03 | 84 | $+477.02 | 47 | 35 | 57% |
| 2026-05-04 | 64 | $+501.20 | 36 | 27 | 57% |
| 2026-05-05 | 72 | $+2,279.49 | 52 | 19 | 73% |
| 2026-05-06 | 64 | $+17.61 | 42 | 22 | 66% |
| 2026-05-07 | 90 | $+1,347.06 | 54 | 34 | 61% |

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

- ⚠️ **WIN_RATE_DROP** · `DOGEUSDT` — last-5 win-rate 0% (< 30%)
- ⚠️ **LOSS_STREAK** · `DOGEUSDT` — 5 consecutive losing SELLs
- ⚠️ **WIN_RATE_DROP** · `PLUMEUSDT` — last-5 win-rate 20% (< 30%)
- ⚠️ **WIN_RATE_DROP** · `TRXUSDT` — last-5 win-rate 20% (< 30%)

## 🛠 Watchlist Suggestions

- Schau dir `DOGEUSDT` an — last-5 win-rate 0% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- `DOGEUSDT` mit 5 consecutive losing SELLs — consecutive_losses-Tracking sollte greifen, aber ein Blick auf die letzten 5 Entry-Reasons kann zeigen, ob ein Pattern-Wechsel im Markt ist.
- Schau dir `PLUMEUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?
- Schau dir `TRXUSDT` an — last-5 win-rate 20% (< 30%). Lohnt sich Symbol-Filter zu prüfen oder pair-spezifischen swarm-vote-Bias zu reviewen?

## 📜 Recent Commits (last 7)

_(git log unavailable)_

## 🚧 Open Sub-Phases (Watch-Files)

- 🚧 `audit/api-version-field-watch.md` (in-progress)
- 🚧 `audit/h4-allocator-scale-watch.md` (in-progress)
- ✓ `audit/h5-lot-size-watch.md` (complete)
- 🚧 `audit/j1-paper-balance-sot-watch.md` (in-progress)
- 🚧 `audit/j2-j4-exit-logic-watch.md` (in-progress)
- 🚧 `audit/j3-macd-confidence-watch.md` (in-progress)
- 🚧 `audit/stage1-7a-vault-pipeline-watch.md` (in-progress)
- ✓ `audit/stage1-7b-auto-context-watch.md` (complete)
- 🚧 `audit/stage1-7c-vault-proposals-watch.md` (in-progress)

## 📍 Where We Left Off

Open anomaly demanding attention: **WIN_RATE_DROP** on `DOGEUSDT`
