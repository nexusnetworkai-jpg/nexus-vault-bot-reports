---
proposal_id: e2e-test-1
title: E2E Test — TP_MAX Round-Trip
created: 2026-05-02
created_by: ethbot-test
status: applied
parameter: TP_MAX
old_value: 0.025
new_value: 0.030
hypothesis: |
  Round-trip end-to-end test for the S1.7c vault-proposals pipeline.
  Verifies validate -> apply -> .env.tuned write -> vault status update.
expected_outcome: Erwarte +$3000 total_pnl ueber 6d Beobachtungs-Window mit TP_MAX=0.030 vs Baseline
target_metric: total_pnl
evaluation_window_days: 0
applied_at: 2026-05-02T11:19:33+00:00
applied_by: ethbot
applied_version: unknown
result: null
---
# E2E Test — TP_MAX Round-Trip

## Hypothesis

Round-trip test for S1.7c vault-proposals pipeline.

## Expected Outcome

Status flips from active -> applied; .env.tuned receives `TP_MAX=0.03`.

## Reasoning

Smoke test for the closed loop after deploy 86c1102.

## Risks

None — paper_mode active, value within safe range.

## Result

(Wird vom Bot nach Apply gefüllt — auto-generated)
