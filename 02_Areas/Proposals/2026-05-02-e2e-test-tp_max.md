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
expected_outcome: |
  Bot reads, validates (TP_MAX in range 0.005-0.20, value 0.03 OK),
  writes TP_MAX=0.03 to .env.tuned, updates this file's status to applied
  with applied_at, applied_by=ethbot, applied_version=<sha>.
target_metric: round_trip_completed
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


## Result (auto-applied by bot)

- when: `2026-05-02T11:19:33+00:00`
- bot version: `unknown`
- effective on next bot restart
