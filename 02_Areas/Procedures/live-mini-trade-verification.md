---
type: procedure
created: 2026-05-08
subject: margin_executor Live-Mini-Trade-Verifikation
trigger: nach Aaron's Margin-Account-Setup, vor Cutover
duration: ~15-30 min
---

# Procedure — margin_executor Live-Mini-Trade-Verifikation

## Voraussetzungen

- [ ] Binance Cross Margin aktiviert
- [ ] API-Key hat Margin-Permission ("Margin Loan, Repay, Transfer")
- [ ] ~$50-100 USDT auf Margin-Account
- [ ] Bot läuft auf Railway, **paper_mode=true** (noch nicht live!)
- [ ] `/api/v3/admin/margin/connection-test` returnt `status=ok` mit
      `borrowEnabled=true`, `tradeEnabled=true`, `ready_for_live_trading=true`

## Procedure (~15 Min)

### Step 1: Connection-Test
```bash
curl -X POST -H "Authorization: Bearer $ADMIN_TOKEN" \
  https://nexus-trading-engine-production.up.railway.app/api/v3/admin/margin/connection-test
```
**Erwartung:** `ready_for_live_trading=true`. Wenn nicht: STOP, debug.

### Step 2: Mini-Position öffnen (manuell, NICHT via Bot)
Auf Binance UI:
- Pair: BTCUSDT
- Mode: Cross Margin
- Position: SHORT
- Notional: ~$5 USDT (kleinster sinnvoller Trade)
- Order-Type: Market
- Bestätigen

**Erwartung:** Position öffnet, Margin-Debt wird erstellt, USDT auf Account leicht erhöht (durch Sell).

### Step 3: 30-60 Sek halten
Beobachte Position-Liste in Binance UI. Verifiziere:
- Position zeigt SHORT
- Liquidation-Price ist weit weg (sollte sein bei nur $5)
- Borrow-Amount ist sichtbar

### Step 4: Manuell schließen (NICHT via Bot)
Binance UI → Margin-Position → Close
- Buy-back + Repay-Borrow automatisch
- USDT-Saldo ~unverändert (minus Fees+Spread = wenige Cents)

### Step 5: Verifikation
- Borrow-Amount = 0
- Position geschlossen
- Margin-Account "clean"

### Step 6: Falls Step 1-5 alle ✓
Dann ist `margin_executor` production-ready. Du kannst `PAPER_MODE=false` flippen wenn bereit (siehe `02_Areas/Procedures/railway-env-vars-cutover.md`).

## Stop-Trigger

- **Step 1 connection-test failed** → API-Setup unvollständig
- **Step 2 Order rejected** → Margin-Account-State falsch (vermutlich Permission)
- **Step 4 Borrow-Amount > 0 nach Close** → Margin-API hat Bug, emergency: manuelles Repay via Binance UI, dann debug
- **Irgendwo ein "Liquidated"-Event** → Setup falsch (Notional war zu groß für Account-Größe)

_Tags: #procedure #pre-cutover #margin-executor #2026-05-08_
