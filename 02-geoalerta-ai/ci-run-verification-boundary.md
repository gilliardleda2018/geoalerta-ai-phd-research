# CI execution evidence — access and verification boundary

**Date:** 2026-10-26. **Source:** private `gilliardleda2018/geoalerta-sul`. Read-only audit. 

## Verified repository declarations

- `.github/workflows/evaluate-critical-event-stage5.yml` runs on pushes to `fix/critical-event-fp-fn-audit`, invokes `python -m scripts.evaluate_critical_event_stage5`, then `pytest tests/test_critical_event_stage5.py -q`, and may commit the decision to that feature branch.
- `.github/workflows/retrain-critical-event-v5.yml` is also feature-branch-scoped and downloads ANA/SGB data, validates source coverage, trains v5 and shadow v6, runs false-alarm evaluation and focused tests, and commits artifacts.
- `tests/test_critical_event_stage5.py` declares three tests (accept only for shadow, reject high FPR, reject mismatched holdout counts). These are test definitions, **not successful test-run evidence**.

## Execution evidence lookup

A read-only lookup of pull-request-triggered workflow runs associated with source commit `a7943f49bd8c3fea15870f8b551e06bb92b61824` returned an empty list. The connector operation is limited to pull-request-triggered runs on the first page; this **does not imply there were no push-triggered runs**, nor that CI failed. An attempted generic GitHub Actions workflows listing was unsupported by the available read interface.

**Conclusion:** no successful CI run ID, job log, artifact digest or tested source SHA has been established in this audit. CI status remains **UNVERIFIED**, not failed or passed.

## Next evidence requirements

1. Obtain the exact GitHub Actions run URL/ID for the two workflows on the feature branch, or a supported connector read for push-triggered runs.
2. Read run conclusion, event type, head SHA, run attempt, job steps, test logs and generated artifact SHA.
3. Confirm whether artifact hashes correspond to current `main` and whether a later commit changed any tested logic.
4. Reproduce focused tests in a disposable environment, capturing Python/pytest versions and exact source commit.
5. Do not trigger retraining automatically: workflow scripts download external data and may commit changes to the feature branch.

## Academic wording

“Automated CI workflows and focused tests are implemented in the repository; successful execution for the audited revision has not yet been independently confirmed.”
