# GeoAlerta Sul — Comparative Model Validation Scorecard

**Evidence status:** Static verification of source documentation, NOT model rerun. **Date:** 2026-10-26. All metrics are repository-reported and should be labelled accordingly in external presentations.

## 1. Two distinct hydrological experiments

| Property | July 2026 model card | Four-event retrospective benchmark |
|---|---|---|
| Source document | `docs/ML_MODEL_CARD.md` | `docs/EVENT_VALIDATION.md` |
| Source Git blob | `12254a9c24269574f9eb398c76aae07dd1f60153` | `aea7158b7e7324cf65175f64c472978eb5b23b08` |
| Data cohort | July 2026, 2,817 quarter-hour origins | Four flood events, 2023–2025 |
| Input coverage | 27 SGB/SACE series | ANA/SGB with discharge features |
| Candidate attributes | 189 | 238 |
| Admitted attributes | 138 | Varies by fold; 170 in Sept 2023 holdout |
| Validation | Temporal holdout in one month | Leave-one-event-out, 3 events train / 1 test |
| Status | Research artifact 2026.07-v3 | Benchmark not promoted |

The 189-versus-238 feature counts are not contradictory: they belong to **different data contracts and evaluation cohorts**. Avoid combining their metrics as if they shared one test population.

## 2. Full six-horizon event benchmark

| Horizon | Hybrid mean RMSE (m) | Worst event RMSE (m) | Persistence mean RMSE (m) | Descriptive relative RMSE reduction | Minimum flood recall |
|---|---:|---:|---:|---:|---:|
| 1h | 0.0804 | 0.0995 | 0.2833 | 71.62% | 0.9962 |
| 2h | 0.1766 | 0.2170 | 0.5644 | 68.71% | 0.9600 |
| 3h | 0.2904 | 0.3839 | 0.8446 | 65.62% | 0.9400 |
| 4h | 0.4030 | 0.5444 | 1.1237 | 64.14% | 0.9200 |
| 5h | 0.5275 | 0.7041 | 1.4019 | 62.37% | 0.8941 |
| 6h | 0.6878 | 0.9263 | 1.6798 | 59.05% | 0.8588 |

Relative reduction is computed from reported aggregate means as `100*(1 - hybrid_rmse/persistence_rmse)`. It is a descriptive ratio, not a paired significance test. Verify the middle-horizon arithmetic before formal typesetting. The report says ExtraTrees won most folds, while hybrid Ridge won some horizons in the 2025 event. Do not claim uniform ExtraTrees superiority.

## 3. Calibration and safety

| Horizon | Raw Brier | Nested calibrated Brier | Screening status |
|---|---:|---:|---|
| 6h | 0.0841 | 0.0740 | Research screening only |
| 12h | 0.0910 | 0.1148 | Research screening; calibration worsened |
| 24h | 0.2939 | 0.1989 | Blocked due to unacceptable false positives |

Source: `docs/CRITICAL_EVENT_CALIBRATION_V2.md`, blob `ffafc505e4291e82d9633448b250ab56a2f08cc3`. This is a separate critical-event probability model and must not be conflated with river-stage regression RMSE.

## 4. Spatial evaluation and negative results

The May 2024 Copernicus EMSR720 three-AOI LOAO benchmark reports 2,016 sampled points, mean baseline ExtraTrees ROC-AUC 0.8444 and enriched 0.8464. Roca Sales degrades (0.7657 to 0.7489); enrichment fails its >=0.02 gain and no-area-loss gate. Lajeado municipal simulated stage maps produce mean static ROC-AUC 0.8007 on 11,235 points; this is map conformance, not independently observed flooding.

## 5. Indicator registry and temporal availability

The July indicator snapshot documents 189 candidates, 147 available and 138 admitted; 51 rejected for insufficient training coverage or constant features. A separate 30-name spatial registry exists. `app/indicators.py` exposes availability, station, feature family, training coverage, admission decision and missing-as-null semantics. These fields are implemented but not yet runtime-tested for this audit.

## 6. What must be independently verified

Dataset hashes; exact train/test origin lists; fold-specific hyperparameters and feature admission; event-level confidence intervals; actual forecast outputs; calibration reliability; provenance of simulated and observed labels; API runtime behavior; code authorship and deployment configuration.

**Academic wording:** “The repository reports retrospective improvements over persistence and explicit negative model-admission outcomes. These results constitute preliminary evidence for a reproducible doctoral investigation; they do not yet establish prospective or cross-basin predictive skill.”

## References

WMO (2025), https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts . Wilkinson et al. (2016), https://doi.org/10.1038/sdata.2016.18 .
