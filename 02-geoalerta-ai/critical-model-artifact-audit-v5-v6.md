# GeoAlerta Sul — Experimental artifact audit: critical v5 versus v6 shadow

**Date:** 2026-10-26. **Source:** private `gilliardleda2018/geoalerta-sul`, branch `main`; exact inspected baseline commit reference `a7943f49bd8c3fea15870f8b551e06bb92b61824` (recheck HEAD at final freeze). This is static analysis of serialized JSON reports, **not independent execution or production verification**. This document may disclose aggregate reported metrics only; do not copy raw private datasets or credentials.

## Artifacts read directly

1. `models/evaluation/critical-events-loeo-v5.json` — four historical flood events, candidate comparisons, folds, quality policies.
2. `models/evaluation/critical-events-non-events-2021-2025-v1.json` — non-event audit of negative origins and false alarm episodes.
3. `models/evaluation/critical-events-v6-shadow.json` — shadow challenger selection and independent 2025 non-event holdout.
4. `models/evaluation/critical-events-stage5-decision.json` — promotion gate decision and comparison.
5. `models/evaluation/ana-events-loeo-v1.json` — hydrological level forecasting leave-one-event-out report and per-event source manifests.

## Stage 5 decision: retrospective rejection

The decision artifact states `stage_status=completed`, `decision=rejected_retrospectively`, `promotion_eligible=false`, `production_change_authorized=false`, and next stage 6 blocked. This is **evidence of a conservative documented decision**, not proof of production safeguards being live.

| Horizon | Baseline false-positive rate (2025) | v6 shadow false-positive rate (2025) | Ratio (v6/baseline) | Baseline persistent false-signal hours | v6 persistent false-signal hours | v6 minimum event recall |
|---|---:|---:|---:|---:|---:|---:|
| 6 h | 0.005545 | 0.048594 | 8.7636 | 43.75 | 388.75 | 0.9583 |
| 12 h | 0.008300 | 0.051904 | 6.2535 | 68.00 | 407.00 | 0.8542 |

**Interpretation:** the v6 challenger passed the configured minimum event-recall criterion of 0.85 but failed false-positive and persistence gates at both horizons. Do not advertise v6 as a superior promoted model. The false-positive rates are per **negative forecast origin**, not per independent non-event episode, and must not be called probability of false public warnings.

## Non-event audit: 2021–2025

The v5 non-event report describes negative origins as current Estrela level below 19 m and future t+h level also below 19 m, excluding known events buffered by 3 days each side; a persistent episode is at least four consecutive positive 15-minute origins. At 6 h it reports 151,535 negative origins, 693 false-positive origins (0.004573), 31 false-signal episodes, 21 persistent episodes and 168.75 persistent false-signal hours. At 12 h it reports 151,202 negative origins, 1,015 false-positive origins (0.006713), 24 episodes, 19 persistent episodes and 251.75 persistent false-signal hours.

These large row counts are correlated and are not equivalent to independent validation events. Review coverage, event buffers, source gaps and definition of negative cases before deriving inferential uncertainty.

## Historical event validation: design caution

The v5 LOEO artifact names four event windows: 2023-09, 2023-11, 2024-05 and 2025-06. Per-fold positive and negative row counts, admitted feature counts, ROC-AUC, average precision, Brier and threshold-dependent recall are recorded. There are meaningful fold differences: for the 6 h logistic model, `recall_at_0_3` equals 1.0 in the 2023-09, 2023-11 and 2024-05 holdouts, but 0.3333 in the 2025-06 holdout. High average metrics must not obscure this event-specific failure mode.

## Scientific implications and next checks

- Analyze false alarms as **episodes**, durations and lead-time tradeoffs, not only per-row false-positive rate.
- Examine threshold selection and leakage: v6 shadow says 2025 negative rows were not used for fitting, calibration, thresholding or selection; verify from scripts and manifest.
- Distinguish independent negative-year holdout from independent positive flood-event holdout; the v6 report itself warns the 2025 negative-year holdout contains an event from the same calendar year.
- Check calibration, base rate, rising-limb misses, and uncertainty across all events.
- Confirm model/version labeling: v5 described as experimental comparison baseline in shadow report, **not necessarily the deployed production incumbent**.
- Inspect exact raw-source hashes, run commands, script revisions and logs; rerun in isolated environment before asserting replication.

## Potential academic contribution

This evidence supports a **testable research agenda** around safety-constrained model promotion, persistent false alarms, uncertainty calibration and event-level generalization. It does not establish a new peer-reviewed method, universal transferability or an official warning service.

## Reference methodology

WMO, *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364 (2025), https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts . For the final submission, verify full bibliographic metadata and relate each metric to its operational interpretation.
