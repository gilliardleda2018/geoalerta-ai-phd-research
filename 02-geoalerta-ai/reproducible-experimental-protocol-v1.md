# GeoAlerta Sul — Preregistered experimental verification protocol (v1)

**Status:** Proposed protocol, not an executed experiment. **Date:** 2026-10-26. **Source of implementation:** private `gilliardleda2018/geoalerta-sul` only. This public document does not contain private code or data. All numerical values below labeled *existing report* are repository-reported, not independently replicated.

## 1. Scientific questions and test units

- **RQ1 Provenance and data fusion:** Does source-aware validation reduce invalid/stale/incorrectly aligned observations versus naive ingestion? Unit: source × station × timestamp × event.
- **RQ2 Predictive skill:** Does hybrid ExtraTrees residual correction outperform persistence and 3-hour trend on entirely held-out flood episodes? Unit: independent event (not 15-minute rows).
- **RQ3 Uncertainty:** Are 90% predictive intervals and critical-threshold probabilities calibrated across events, including rising limbs? Unit: event × horizon.
- **RQ4 Portability:** Can adapters and municipal configuration transfer to a second city/basin with documented engineering cost and externally validated skill? Unit: independent municipality/basin.
- **RQ5 Human decision support:** Can provenance and uncertainty explanations improve detection of unreliable forecasts under blinded scenario evaluation? Unit: participant × scenario; no live warning or evacuation.

## 2. Reproducibility freeze (required before running)

Record exact source commit SHA, branch, UTC timestamp, Python and package versions, OS/container digest, command line, random seeds, data license, raw and normalized SHA-256, source and publication timestamps, sensor IDs, coordinate reference systems, vertical datum, thresholds, event IDs, exclusions, artifact digest and hardware. Archive immutable training/holdout manifests with access controls. No raw private dataset in public dossier.

## 3. Cohorts and leakage prevention

Use documented Taquari events September 2023, November 2023, May 2024 and June 2025 as initial *event* folds, and July 2026 as an independent retrospective test if untouched by tuning. Add non-event windows with defined sampling and independent provenance. For each fold, train on complete other events only; construct lags/targets within event boundaries; fit imputers, feature admission, scaling, hyperparameters and calibration only on training folds. A truly prospective untouched test is still needed. State the origin and publication time for every feature; no future observed rainfall or post-event damage in predictors. Distinguish row count from independent event count. Predefine whether expanding-window folds are used; do not train on later events when claiming strictly chronological forecasting.

## 4. Models and controls

**River-stage regression:** persistence; linear trend 3h; Ridge; HistGradientBoosting; ExtraTrees hybrid residual correction. Same origins, thresholds and event folds, with preprocessing learned on train only. Report all models, not only the best.

**Critical event:** balanced logistic regression, ExtraTrees and boosting; predict crossing of local 19m reference at 6, 12 and 24h. The 19m threshold is local, not universal. 24h remains blocked for operational decisions until calibration and reliability gates pass.

**Context models:** GOES, ECMWF and NVIDIA Earth-2 are separately documented contextual sources until source-admission tests show prospective gains. Do not attribute their presence to an improvement in the primary model without ablation and approved promotion.

## 5. Primary metrics, precommitted analyses

- Regression: event-wise MAE, RMSE, signed bias, rising-limb/peak timing and magnitude error, and skill relative to persistence; report distribution and worst event.
- Exceedance: event-wise sensitivity, precision, false alarms per non-event period, first valid lead time, PR-AUC, ROC-AUC, Brier score and reliability diagram; prevalence and decision threshold fixed from train only.
- Uncertainty: empirical coverage of 90% interval, width, conditional coverage by horizon and event; where probabilistic samples exist report CRPS. Intervals calibrated on a separate training-only calibration set.
- Resilience: acceptance of stale/future/duplicate/mixed-datum data, percent withheld safely, recovery time and forecast degradation under controlled outages.
- Statistical uncertainty: event-level bootstrap where independent episodes suffice; with four events, present individual outcomes and acknowledge imprecise confidence intervals instead of overstating significance.
- Portability: number of adapters, engineering hours, completeness of local source mappings, validation data availability, performance shift and recalibration effort.

## 6. Gates: safety vs scientific success

Safety **must** be demonstrated before any decision-facing test: no zero-imputation of missing observations, no cross-datum averaging, stale-data rejection, provenance present, no public-warning authorization, human review mandatory. Scientific performance criteria (e.g. improvement over baseline, interval coverage, event recall) must be specified before examining the final holdout and negotiated with domain experts; avoid selecting a threshold after observing results. No automatic emergency action is within scope.

## 7. Existing implementation traceability (paths, not execution proof)

| Experimental stage | Candidate evidence in GeoAlerta Sul |
|---|---|
| Raw event acquisition | `scripts/download_ana_events.py`, `scripts/download_ana_hydrowebservice.py` |
| Baseline | `scripts/backtest_baseline.py`, `app/forecasting.py`, `tests/test_forecasting.py` |
| ML training | `scripts/train_ml_models.py`, `app/ml_training.py` |
| Leave-one-event-out | `scripts/evaluate_ana_events.py`, `models/evaluation/ana-events-loeo-v1.json` |
| Critical classification | `scripts/train_critical_event_model.py`, `app/event_model.py` |
| Independent critical test | `scripts/evaluate_critical_event_2026_07.py`, `models/evaluation/critical-events-holdout-2026-07.json` |
| Non-event controls | `scripts/evaluate_non_event_false_alarms.py`, `models/evaluation/critical-events-non-events-2021-2025-v1.json` |
| Calibration/shadow | `app/target_calibration.py`, `app/shadow_model.py`, `models/evaluation/critical-events-v6-shadow.json` |
| Geographic layers | `scripts/calibrate_topography_emsr720.py`, `models/evaluation/topography-emsr720-loao.json` |
| Earth-2 context | `scripts/build_earth2_context.py`, `tests/test_earth2_weather.py` |

**Next action:** inspect complete code and manifests; derive exact executable commands and check prerequisites in isolated environment. Do not run production jobs, write to AWS, or promote models under this protocol.

## 8. Scientific methodological references

[1] World Meteorological Organization, *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364, 2025. https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts (accuracy, bias, reliability, resolution and sharpness).

[2] *A methodological framework for the evaluation of short-range flash-flood hydrometeorological forecasts at the event scale*, *Natural Hazards and Earth System Sciences*, 23, 2001–2023 (2023). https://nhess.copernicus.org/articles/23/2001/2023/ (event-specific and spatial/temporal limitations).

[3] Wilkinson et al., *The FAIR Guiding Principles for scientific data management and stewardship*, *Scientific Data* 3, 160018 (2016), https://doi.org/10.1038/sdata.2016.18 (provenance and reproducibility framing).

**Reference check:** complete author metadata and citation formatting before formal submission.
