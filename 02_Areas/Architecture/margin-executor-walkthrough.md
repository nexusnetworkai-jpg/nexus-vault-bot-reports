---
type: architecture
created: 2026-05-08
subject: bot/margin_executor.py Code-Walkthrough für Aaron
purpose: Verständnis-Reference für Cutover-Moment
---

# Architecture — margin_executor Code-Walkthrough

## Was margin_executor tut

Custom HMAC-signed Calls zu Binance Spot Cross Margin API (`/sapi/v1/margin/order`). Nutzt `sideEffectType=AUTO_BORROW_REPAY` für seamless borrow-and-sell. Reines `requests.Session` + custom HMAC-SHA256 — KEIN python-binance margin-API.

Files:
- `bot/margin_executor.py` (361 Zeilen)
- Class: `MarginClient`
- Singleton: `get_margin_client()`

## Public API (5 main entry points)

### `open_short(symbol, qty, stop_loss_pct=2.0)`
1. Berechne notional in quote-currency (qty × spot-price)
2. Safety-Check: notional ≤ `MAX_POSITION_USD` (50.000) — €1k initial irrelevant
3. Safety-Check: `margin_level > 2.0` (verhindert über-leveraged neue position)
4. POST `/sapi/v1/margin/order` mit `sideEffectType=AUTO_BORROW_REPAY` + `autoRepayAtCancel=true`
5. Margin-Account borrowt automatisch base-currency und verkauft sie
6. Setze STOP_LOSS_LIMIT bei `entry × (1 + stop_loss_pct/100)` als Safety-Net (-2% adverse)
7. Return order-response (None on failure)

### `close_short(symbol, qty)`
1. POST `/sapi/v1/margin/order` mit `side=BUY` + `sideEffectType=AUTO_BORROW_REPAY`
2. Margin-Account kauft base-currency zurück und repays borrow automatisch
3. Return order-response

**§B-S2 Fix (2026-05-08):** Pre-fix wurde `close_short` NIE aufgerufen — der engine close-pfad routet jetzt korrekt: `_close_position_routed(...)` checked `pos.direction == "SHORT"` und routet entsprechend.

### `open_long(symbol, qty)`, `close_long(symbol, qty)`
Symmetrische Long-Pfade auf Margin (S5_LiqHunter könnte das nutzen). Aktuell vom hot-path NICHT aufgerufen — Long-Pfad geht über Spot via `executor.execute_buy/sell`.

### `close_all_positions()` (Emergency)
Iteriert alle borrowed userAssets, repays jeden mit interest. Für emergency-stop-trigger.

## Risk-Architektur

| Layer | Beschreibung |
|---|---|
| `AUTO_BORROW_REPAY` | Bot muss nicht manuell borrow/repay tracken — Binance erledigt's |
| `STOP_LOSS_LIMIT` (-2%) | Worst-Case max -2% pro Trade. Triggert auch wenn Bot stirbt |
| `autoRepayAtCancel=true` | Wenn STOP_LOSS canceled wird → auto-repay debt |
| `MAX_POSITION_USD=50000` | Hard-Cap, irrelevant in Phase A (€1k) |
| `margin_level > 2.0` Pre-Check | Neue SHORTs nur wenn account gesund |

## API-Key-Permissions benötigt

- ✓ Spot & Margin Trading
- ✓ Margin Loan, Repay, Transfer
- ✗ **NICHT** Withdrawal (niemals enable)

## Failure-Modes

| Failure | Behavior | Cutover-Risk |
|---|---|---|
| API-Auth-Error | `close_short` returnt None → engine retry next loop (B-S2 invariant schützt state) | Niedrig |
| Insufficient Margin | STOP_LOSS_LIMIT triggert bei -2% adverse | Niedrig (capped loss) |
| Network-Timeout | Retry-Logic via internal `_request` retry (3 attempts) | Sehr niedrig |
| Margin-Account nicht aktiv | `get_account()` returnt None → `connection-test` endpoint zeigt's vor Cutover | Niedrig (catch pre-deploy) |
| Liquidation-Cascade | margin_level fällt unter 1.3 → Binance liquidiert automatisch | **Hoch** wenn LIVE_CAPITAL zu klein vs Position-Size — Mitigation via `MAX_POSITION_USD` + `RISK_PER_TRADE=0.01` |

## Pre-Cutover-Verifikation (Procedure-Refs)

1. `02_Areas/Procedures/live-mini-trade-verification.md` — manuelle $5-Mini-Trade-Verifikation
2. `/api/v3/admin/margin/connection-test` — automatischer health-check
3. `/api/v3/admin/position-audit` — state-vs-Binance diff

_Tags: #architecture #margin-executor #pre-cutover #2026-05-08_
