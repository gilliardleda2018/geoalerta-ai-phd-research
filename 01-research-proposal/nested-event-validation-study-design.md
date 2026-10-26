# Study Design — Nested Event-Level Validation for Flood Forecasting

**Status:** preregistered-style proposed study, not executed. **Source project:** GeoAlerta Sul only.

## Primary objective

Estimate predictive skill of an algorithm-selection process without using outer test labels to choose the algorithm. Compare against persistence and linear trend on the same event origins.

## Data and folds

Initial events: Taquari September 2023, November 2023, May 2024 and June 2025. Outer loop holds one entire event out. Inner loop selects among persistence, linear trend, hybrid Ridge, hybrid ExtraTrees and hybrid HistGradientBoosting using only the other three events. Feature admission, imputation and hyperparameter selection are fitted within each inner training fold. Retrain selected model on three outer-training events and evaluate once on the fourth.

Four events are a feasibility benchmark, not sufficient to establish broad generalization. An additional forward-time event and second-basin evaluation are planned. Avoid leakage through timestamps, post-event outcomes, source publication delays and cross-event rolling windows.

## Primary outcomes

Per-event and per-horizon RMSE, MAE and bias; secondary outcomes include peak-level error, event recall, precision, false-alarm episodes, lead time and empirical interval coverage. Compare with persistence and trend on matched origins. Preserve event-level rows and publish aggregate results only when legally permitted.

## Uncertainty and negative results

Report all outer folds individually. Do not infer statistical significance from 15-minute rows as independent samples. If event-block confidence intervals are unstable due to only four events, disclose this and defer significance claims. Track model-selection stability and failures.

## Reproducibility

Freeze code SHA, dependencies, dataset manifests, original SHA-256, source station mapping and vertical datum. Preserve exact commands and logs in an isolated environment without production credentials. No model promotion or emergency-warning authorization follows from this study.

## Literature anchors

WMO, Guidelines on the Verification of Hydrological Forecasts (2025), https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts ; Wilkinson et al., FAIR Principles (2016), https://doi.org/10.1038/sdata.2016.18 .

**Related finding:** `02-geoalerta-ai/loeo-model-selection-bias-critical-audit.md`.
