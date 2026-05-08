---
type: procedure
created: 2026-05-08
subject: Railway-env-vars für PAPER_MODE=false-Switch
trigger: nach erfolgreichem Live-Mini-Trade
duration: ~5 min
---

# Procedure — Railway-env-vars-Cutover

## Reihenfolge KRITISCH (Auto-Redeploy bei jeder Variable-Änderung)

Setze diese Variablen in **EINEM Save-Vorgang** (Railway erlaubt Multi-Edit), sonst triggert jede Einzeländerung einen separaten redeploy.

| Variable | Neuer Wert | Begründung |
|---|---|---|
| `LIVE_CAPITAL` | `1000` | Initial €1k aus Stake-Cap-Decision |
| `RISK_PER_TRADE` | `0.01` | 1% per trade (~1/25 Kelly) |
| `MAX_DAILY_LOSS_PCT` | `3.0` | 3% daily-loss-stop |
| `MAX_DRAWDOWN_PCT` | `10.0` | 10% emergency-stop |
| `PAIRS` | `ETHUSDT,BTCUSDT,ORCAUSDT` | Initial-3 Pair-Staffel (Tag 1-7) |
| `PAPER_MODE` | `false` | **THE SWITCH** |

**WICHTIG:** `PAPER_MODE=false` als LETZTES setzen oder gleichzeitig mit den anderen. Reihenfolge: erst andere, dann `PAPER_MODE`.

## Sofort nach Save:
- Railway redeployt (~2-3 min)
- Polling: `curl /api/v3/status` bis `version` matched neuen HEAD

**Erwartete neue State:**
- `paper_mode: false` (jetzt!)
- `is_running: true`
- `paper_balance: 1000.00` (hatte vorher 133k aus Paper)
- `total_pnl: 0.00` (frisch)
- `total_trades: 0` (frisch)

## Falls etwas nicht stimmt — Rollback in 30 Sek:
1. Railway → Variables → `PAPER_MODE` → `true`
2. Save → Auto-Redeploy
3. Bot ist zurück in Paper

## Watch-Window (siehe Pre-Mortem)
- **Erste 1h:** ich sitze davor, alle 5min `/api/v3/status` check
- **Erste 6h:** alle 30min
- **Erste 24h:** stündlich, jede Telegram-notification gegenprüfen
- **Erste 24h spezifisch:** ersten LIVE-SHORT-Trade pro Pair manuell verifizieren

_Tags: #procedure #pre-cutover #env-vars #cutover-day #2026-05-08_
