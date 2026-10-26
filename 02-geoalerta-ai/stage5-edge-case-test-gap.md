# GeoAlerta Sul — Stage 5 edge-case and test-coverage audit

**Date:** 2026-10-26. **Source:** private `gilliardleda2018/geoalerta-sul`, `scripts/evaluate_critical_event_stage5.py`, `tests/test_critical_event_stage5.py`, `config/critical_event_promotion_gates.json`. **Method:** source-level inspection, no test execution and no modification to source or production. This report is not evidence of a live production defect.

## Finding GA-SUL-TEST-001: unguarded zero denominators

In `evaluate_stage5`, the code computes three ratios:

- `candidate["holdout_false_positive_rate"] / baseline_holdout["false_positive_rate"]`
- `candidate["holdout_false_alarm_episodes"]["persistent_false_signal_hours"] / baseline_holdout["persistent_false_signal_hours"]`
- `candidate["holdout_false_alarm_episodes"]["maximum_episode_hours"] / baseline_holdout["maximum_episode_hours"]`

There is no explicit zero-denominator branch. If a baseline metric is zero, Python division raises `ZeroDivisionError`, rather than returning a documented fail-closed gate decision. Current serialized Stage 5 inputs have positive denominators and the stored rejection decision is arithmetically consistent; this finding concerns **edge-case robustness**, not the reported v5/v6 result.

**Recommendation (not implemented):** specify semantics for (baseline=0, challenger=0) and (baseline=0, challenger>0). The latter must never be treated as improvement; for both zero, consider explicit equality/absolute burden policy rather than a meaningless ratio. Ensure the result is an auditable blocked/eligible state or a controlled input-validation error, with no accidental promotion. Require scientific and domain review before changing the gate definition.

## Finding GA-SUL-TEST-002: current unit-test coverage boundary

Three tests currently cover: successful gate set => **eligible for prospective shadow only**; high FPR => retrospective rejection; mismatched negative holdout population => `ValueError`. They do not directly test: zero denominators, missing horizon, absent holdout year, missing keys, NaN/infinite values, negative hours, malformed recall or duplicate horizon entries. These are candidate robustness tests, not confirmed failures.

## Proposed test matrix (not executed)

| Test ID | Input variation | Expected behavior to define |
|---|---|---|
| T01 | Baseline FPR=0, challenger FPR>0 | Block or controlled validation error; never pass |
| T02 | Both FPR=0 | Explicit policy handling; no divide-by-zero |
| T03 | Baseline persistent hours=0 | Explicit policy handling |
| T04 | Baseline max episode hours=0 | Explicit policy handling |
| T05 | Missing required horizon | Controlled schema error; never promote |
| T06 | Missing holdout year | Controlled schema error; never promote |
| T07 | NaN/Infinity/negative metric | Reject invalid metric |
| T08 | Recall outside [0,1] | Reject invalid metric |
| T09 | Duplicate horizon in JSON | Reject ambiguous report |
| T10 | All gates pass | Shadow eligibility only; production authorization false |

## Research relevance

This finding illustrates the distinction between a *declared safety policy*, its *implementation*, and empirical *assurance under adversarial or anomalous inputs*. The doctoral dossier may cite it as an engineering audit observation, not as a novel AI method or proof of operational risk.

## Evidence needed for closure

A minimal isolated test run with source commit SHA, environment, input fixtures, stdout/exit codes and reviewer; reviewed policy semantics; tests and CI run proof. Do not modify GeoAlerta Sul without a separate implementation approval.
