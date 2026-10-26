# Independent arithmetic verification and CI traceability — GeoAlerta Sul Stage 5

**Date:** 2026-10-26. **Method:** read-only GitHub inspection and separate JavaScript recalculation of ratios from source JSON; **no ML training or tests executed**, no deployment changed. Source: private `gilliardleda2018/geoalerta-sul` main. Previous reference HEAD: `a7943f49bd8c3fea15870f8b551e06bb92b61824`; re-freeze before formal submission.

## Inputs
- `models/evaluation/critical-events-non-events-2021-2025-v1.json`
- `models/evaluation/critical-events-v6-shadow.json`
- `config/critical_event_promotion_gates.json`
- `models/evaluation/critical-events-stage5-decision.json`
- `scripts/evaluate_critical_event_stage5.py`
- `tests/test_critical_event_stage5.py`
- `.github/workflows/evaluate-critical-event-stage5.yml`
- `.github/workflows/retrain-critical-event-v5.yml`

## Recalculation

| Horizon | FPR ratio | Persistent false signal hours ratio | Maximum false episode hours ratio | Independent decision | Stored decision |
|---|---:|---:|---:|---|---|
| 6h | 8.763570784490533 | 8.885714285714286 | 2.6986301369863015 | fail | fail |
| 12h | 6.2534939759036146 | 5.985294117647059 | 2.230769230769231 | fail | fail |

Both compared negative holdout populations match in count. Gate limits: FPR ratio <=0.8; persistent signal hours ratio <=0.8; maximum episode hours ratio <=1.0; minimum event recall >=0.85. Recall passes both horizons, but all three false-alarm gates fail. Arithmetic and stored decision **agree**.

**Verification level:** independent recomputation of deterministic ratios and decision from serialized values. **Not** independent replication of underlying model inference, raw observations, calibration or true operational outcomes.

## CI workflow observations

The Stage 5 workflow runs only on pushes to `fix/critical-event-fp-fn-audit` and explicitly checks out that branch, runs the decision script, runs three focused pytest cases and commits the decision to that branch. Its existence on `main` does not show it ran successfully against `main` or that production uses the resulting artifact.

The retraining workflow also targets the same fix branch. It downloads four ANA flood events, SGB observations and continuous 2021–2025 ANA history, checks target coverage (all four events; at least five usable ANA stations each), trains v5 and v6 shadow, evaluates false alarms, rejects stale SGB for shadow refresh, runs focused safety tests and commits artifacts. These are **workflow declarations**, not observed successful runs.

## Issues for next audit

1. Retrieve workflow-run IDs, conclusions, commits, artifacts and logs (read-only) to establish actual execution.
2. Compare workflow branch and artifacts to current `main` revision; avoid assuming branch parity.
3. Verify that downloaded upstream ANA/SGB data correspond to the recorded manifests and hashes.
4. Reproduce targeted unit tests in an isolated, pinned environment; capture stdout, exit code and versions.
5. Audit missing and zero-denominator handling in `evaluate_stage5` for edge cases not covered by current tests.
6. Record an explicit source-code provenance and individual contribution map for the applicant.

## Citation and claim policy

Label this finding **independently recalculated decision logic from repository-reported metrics**, not independently reproduced scientific performance. No assertion of live production activation or official public-warning capability.
