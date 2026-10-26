# GeoAlerta Sul — Scientific and Technical Report
## Chapter 2: Experimental Results, Failure Analysis and Reproducibility

**Version:** 2026-10-26 research manuscript v2. **Status:** draft for scientific review, not independently reproduced, not submitted.  
**Primary source:** private `gilliardleda2018/geoalerta-sul`. All original model metrics are **repository-reported**; secondary calculations are explicitly labelled.

### Abstract
This chapter distinguishes four experimental constructs: continuous river-stage regression, threshold-crossing probabilities, spatial flood delineation discrimination, and municipal simulated-stage conformance. They must not be merged into a generic flood-accuracy score. The most important empirical finding is the combination of promising retrospective candidate performance with documented failures of model promotion, threshold selection and report reconciliation. These issues define a concrete, falsifiable doctoral validation program.

## 1. Research objects and estimands

| Experiment | Target | Unit of independence | Principal metrics | Evidence limitation |
|---|---|---|---|---|
| River-level regression | Estrela gauge stage 1–6h ahead | Held-out flood event | RMSE, MAE, bias, flood recall | Four events in one basin; post-hoc winner selection |
| Critical-event classification | First crossing of local 19m threshold | Event and non-event episode | Brier, recall, AP, false alarms | Calibration varies by horizon |
| Copernicus spatial comparison | Delineated flood extent by AOI | Held-out area/event | ROC-AUC, AP, balanced accuracy | Three AOIs from one 2024 event; sampled negatives |
| Municipal stage conformance | Agreement with simulated flood-stage polygons | Stage and independent geography | ROC-AUC, precision, recall | Simulated reference; optimized cutoff uses test labels |

## 2. Hydrological forecast skill

### 2.1 Two different datasets
The July 2026 model card describes 2,817 quarter-hour origins, 27 SGB/SACE series, 189 candidate attributes and 138 admitted. The four-event ANA/SGB benchmark contains 238 candidate features, evaluated on September 2023, November 2023, May 2024 and June 2025. These are **not paired cohorts** and their RMSE differences cannot be interpreted as evidence of temporal deterioration without controlling dataset and method.

### 2.2 Fixed-candidate sensitivity analysis
The `ana-events-loeo-v1.json` artifact stores candidate metrics for each held-out event. The academic arithmetic audit computed unweighted event-mean RMSE for a **fixed ExtraTrees candidate** and persistence:

| Horizon | Persistence mean RMSE (m) | Fixed ExtraTrees mean RMSE (m) | Descriptive reduction |
|---|---:|---:|---:|
| 1h | 0.283325 | 0.081700 | 71.16% |
| 2h | 0.564450 | 0.175900 | 68.84% |
| 3h | 0.844625 | 0.293500 | 65.25% |
| 4h | 1.123725 | 0.408950 | 63.61% |
| 5h | 1.401925 | 0.537525 | 61.66% |
| 6h | 1.679750 | 0.697200 | 58.49% |

**Figure E1:** [Forecast RMSE across six horizons](figures/forecast-rmse-six-horizon-v2.svg). The fixed candidate was examined *retrospectively*; its comparison is exploratory, not a preregistered model-choice policy.

### 2.3 The critical selection-bias finding
The source function `app/ml_training.py::evaluate_event_holdout` trains candidate models without the outer held-out flood, but subsequently selects the best candidate by the same flood's RMSE. Consequently, the stored `selected_model` summary is an **oracle-style, post-hoc model-selection result**. It does not estimate the prospective performance of an algorithm chosen without access to outer test labels. The fixed ExtraTrees sensitivity analysis reduces this direct per-event switching, but does not remove the fact that the candidate was examined after earlier reports were known.

**Required repair:** nested event validation. For each outer event, select preprocessing, feature admission, algorithm and hyperparameters by inner folds using the other three events; refit on those three events and evaluate once on the untouched fourth. Report the complete outer-event table, not only a mean.

### 2.4 Numerical report discrepancy
The narrative `docs/EVENT_VALIDATION.md` reports selected-model mean RMSE of 0.0804m (1h) and 0.6878m (6h). Direct arithmetic means of four `selected_model` rows in `ana-events-loeo-v1.json` are 0.078525m and 0.677475m. The difference occurs across all six horizons. A separate pooled, valid-origin-weighted RMSE computation did **not** reconcile the numbers. The underlying cause is unknown: do not assert a changed training run or typo without version evidence. Preserve both source artifacts and identify the exact metric source in every submitted figure.

## 3. Probabilistic critical-event prediction

The critical target is crossing the local Estrela 19m stage within a horizon when the gauge starts below threshold. Repository-reported nested calibration gives:

| Horizon | Raw Brier | Calibrated Brier | Relative change | Negative-origin false-positive fraction | Status |
|---|---:|---:|---:|---:|---|
| 6h | 0.0841 | 0.0740 | −12.01% | 0.1511 | Research screening |
| 12h | 0.0910 | 0.1148 | +26.15% | 0.0630 | Research screening; worsened |
| 24h | 0.2939 | 0.1989 | −32.32% | 0.6455 | Blocked |

The Brier score measures squared probability error, not alert usefulness alone. The 24h calibration decreases Brier but remains blocked because the false-positive burden is unacceptable. The v6 shadow challenger was likewise rejected at 6h and 12h despite minimum recall meeting its gate. This is an important *negative result and governance behavior*, not evidence that deployed warnings are safe.

The non-event script groups predicted positive origins within 30 minutes and marks four or more positive origins as a persistent episode. Its signal-hour metric counts positive origins × 0.25h; irregular timestamp gaps can make this different from actual elapsed alert duration. A replication must quantify both with a declared gap policy.

## 4. Spatial discrimination: Copernicus EMSR720

The project samples 2,016 comparison points across Encantado (736), Roca Sales (1,016) and Santa Tereza (264), including 504 delineated positives and 1,512 comparison negatives. The resulting 25% positive prevalence is imposed by the sampling procedure, not an estimate of the true inundated fraction of land.

| Held-out AOI | Baseline ExtraTrees ROC-AUC | Enriched ROC-AUC | Delta |
|---|---:|---:|---:|
| Encantado | 0.8491 | 0.8666 | +0.0175 |
| Roca Sales | 0.7657 | 0.7489 | −0.0168 |
| Santa Tereza | 0.9184 | 0.9237 | +0.0053 |
| Mean | 0.8444 | 0.8464 | +0.0020 |

The enriched candidate fails the declared mean-gain ≥0.02 and no-AOI-loss gate. All AOIs belong to the same 2024 event, so this is within-event geographic holdout rather than cross-event or cross-basin generalization. Non-delineated pixels may be unobserved rather than definitely dry.

The Copernicus EMSR720 activation and its flood-delineation products are independently documented by the official service [E1,E2]. That official documentation validates the existence of the mapping products, **not GeoAlerta's model performance**.

## 5. Municipal simulated-stage conformance

The Lajeado study compares a terrain score to 18 simulated stage maps across 11,235 points. Mean reported static ROC-AUC is 0.8007. At the 21m simulated stage, the rule reports 100% recall and 1.92% precision; high recall coexists with substantial positive overclassification relative to the simulated reference. The optimized classification threshold is selected on the evaluation labels themselves, making the optimized-cutoff metrics in-sample. Stages 31m and 33m require geometry inspection. No causal impact on public safety is inferred.

## 6. Scientific conclusions and publication boundaries

The repository contains a substantive experimental research platform, but the strongest scientific statement today is about **research readiness and auditable limitations**, not a verified universal flood predictor. Future work must establish raw-data integrity, event-independent selection, reliability under missing sensors, spatial prevalence robustness and cross-city transfer.

**Evidence classes:** (i) code-inspected mechanisms, (ii) repository-reported metrics, (iii) independently recalculated arithmetic, (iv) proposed but not yet executed experiments. Every figure and table must state its class.

### Public sources
[E1] Copernicus EMS GloFAS, *Rio Grande do Sul Flooding, Brazil — April to June 2024*, 13 June 2024: https://global-flood.emergency.copernicus.eu/news/170-rio-grande-do-sul-flooding-brazil-april-to-june-2024/  
[E2] Copernicus EMSR720, *Flood in Rio Grande do Sul State, Brazil*, situational reporting, 7 August 2024: https://storymaps.arcgis.com/stories/0bfb6520fbb9485e8b5acc5900b44787  
[E3] WMO, *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364, 2025: https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts

### Internal primary evidence
`02-geoalerta-ai/fixed-extratrees-event-recalculation-and-reconciliation.md`; `full-six-horizon-report-reconciliation.md`; `loeo-model-selection-bias-critical-audit.md`; `data-lineage-calibration-false-alarms-audit.md`; `critical-model-artifact-audit-v5-v6.md`; `independent-municipal-event-validation-protocol.md`. Technical origin: private `geoalerta-sul` `models/evaluation/ana-events-loeo-v1.json`, `docs/EVENT_VALIDATION.md`, `docs/CRITICAL_EVENT_CALIBRATION_V2.md`, `models/evaluation/topography-emsr720-loao.json`.
