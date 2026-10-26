# GeoAlerta Sul — Independent event and municipality validation design, with reported stage-level diagnostics

**Date:** 2026-10-26. **Status:** research protocol and static audit, not an executed independent experiment. **Exclusive source:** private `gilliardleda2018/geoalerta-sul` (main; freeze SHA before final submission). Original source repository remains read-only.

## 1. Why municipal-stage agreement is not external validation

The 18-stage Lajeado report compares scores against municipal **simulated** flood-stage polygons on 11,235 grid points. It is a *conformance study*. It does not establish predictive performance against independently observed inundation, because map production and score calculation can share topographic assumptions. The separately reported Copernicus EMSR720 three-AOI experiment uses observed delineations, but all AOIs are upstream (Encantado, Roca Sales, Santa Tereza) and within the same May 2024 flood event. Neither is an out-of-basin generalization test.

## 2. Stage-level reported results: sensitivity versus precision

Source: `models/evaluation/lajeado-municipal-stages.json`, fields `grid_positive`, `risk_class_precision`, `risk_class_recall`, `static_roc_auc`. Percentages below are directly converted from stored fractions; no new model run.

| Simulated stage | Reference positive points / 11,235 | Reference prevalence | Risk-class precision | Risk-class recall | Static ROC-AUC |
|---|---:|---:|---:|---:|---:|
| 19 m | 97 | 0.86% | 1.78% | 92.78% | 0.8005 |
| 21 m | 195 | 1.74% | 1.92% | 100.00% | 0.7865 |
| 25 m | 516 | 4.59% | 5.09% | 100.00% | 0.7927 |
| 30 m | 1,265 | 11.26% | 12.46% | 99.92% | 0.8057 |
| 35 m | 1,846 | 16.43% | 18.15% | 99.73% | 0.8028 |
| 40 m | 2,372 | 21.11% | 23.29% | 99.62% | 0.7709 |

The dynamic uniform stage rule saturates at stages >=21 m, flagging 10,144/11,235 points (90.29%). Therefore high recall is accompanied by low positive predictive value, particularly at low simulated flood prevalence. These values are **not** false public warning rates or observed casualty outcomes.

## 3. Geometry quality assurance

The report marks 31 m and 33 m for manual geometry review: a >75% mapped-area increase at 31 m and a decrease from 32 to 33 m. Do not use these stages as automatic calibration labels until source geometry, projection, stage definitions and polygon topology are reviewed. A non-monotonic area may be caused by product versioning or geometry differences; the cause has not been established.

## 4. Copernicus EMSR720 spatial holdout

The separate `topography-emsr720-loao.json` report records 2,016 comparison points (504 positive, 1,512 comparison negative), 90 m sampling, and leave-one-area-out ExtraTrees evaluations.

| Held-out AOI | N | Positives | Baseline ROC-AUC | Enriched ROC-AUC | Baseline AP | Enriched AP |
|---|---:|---:|---:|---:|---:|---:|
| Encantado | 736 | 184 | 0.8491 | 0.8666 | 0.6218 | 0.6227 |
| Roca Sales | 1,016 | 254 | 0.7657 | 0.7489 | 0.4807 | 0.4673 |
| Santa Tereza | 264 | 66 | 0.9184 | 0.9237 | 0.7170 | 0.7260 |

Mean ROC-AUC: 0.8444 baseline, 0.8464 enriched (+0.0020). Enrichment fails the declared >=0.02 mean-gain and no-AOI-loss promotion gate. All three areas belong to the same event and basin; the 25% positive test prevalence is a designed 3:1 negative sample, not geographic prevalence.

## 5. Preregistered independent validation protocol

**D1 — Source and labels:** obtain permitted observed flood extents, station records and surveyed high-water marks; retain source license, acquisition/issue time, uncertainty, original hash and normalized hash. Treat unlabeled areas as unknown where remote sensing coverage is incomplete.

**D2 — Geography:** designate independent municipality/area holdouts (e.g. Lajeado/Estrela if independently observed labels become available), then an out-of-basin municipality with separate station/vertical-datum mappings. No feature or threshold selection using final test labels.

**D3 — Time:** reserve whole flood events for forward-time validation; separate May 2024 EMSR720 geography from independent 2023/2025 event observations. No random splitting of spatially autocorrelated points.

**D4 — Baselines:** physical HAND/distance/slope heuristic; ExtraTrees baseline; enriched ExtraTrees; simple logistic or calibrated baseline if justified. Keep feature extraction, label support and sampling identical per comparison.

**D5 — Metrics:** per-event and per-municipality confusion matrices (TP, FP, TN, FN); precision, recall, specificity, F1, balanced accuracy, ROC-AUC, average precision, Brier/reliability when outputs are probabilities, spatial IoU and mapped-area bias where polygons support it. Report positive prevalence and explicit denominators.

**D6 — Uncertainty:** AOI/event-block uncertainty, sensitivity to sensor/label errors, class prevalence, threshold and resolution; avoid treating neighboring 90 m points as independent observations.

**D7 — Acceptance:** no automatic promotion; review independently observed performance, calibration, false alarms, data licensing and human safety governance. Portability is assessed by adapter completeness, engineering cost and validated predictive performance, not by the ability to load another map.

## 6. Traceability and non-sensitive public sources

- GeoAlerta Sul `scripts/evaluate_municipal_stages.py`, `models/evaluation/lajeado-municipal-stages.json`, `docs/MUNICIPAL_STAGE_CONFORMANCE.md`.
- GeoAlerta Sul `scripts/calibrate_topography_emsr720.py`, `models/evaluation/topography-emsr720-loao.json`, `docs/TOPOGRAPHY_CALIBRATION.md`.
- Copernicus EMS portal: https://rapidmapping.emergency.copernicus.eu/ (identify EMSR720 and AOI product version in final dataset citation).
- SGB/SACE: https://www.sgb.gov.br/sace/ ; ANA: https://www.ana.gov.br/hidrowebservice/swagger-ui/index.html .
- WMO, *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364 (2025): https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts .

These links are public entry points, not evidence that specific raw files may be redistributed. Keep private telemetry, restricted geodata and business-sensitive implementation artifacts out of the public dossier.

## 7. Claim language for UPC

**Defensible:** “GeoAlerta Sul has been retrospectively compared against municipal simulated flood-stage maps and against Copernicus flood delineations in three held-out upstream AOIs. The current stage-rule conformance exhibits high sensitivity but low precision at lower simulated stages; enriched topographic features were not promoted because improvement was inconsistent.”

**Not defensible yet:** “The system has independently validated flood-inundation predictions for any city” or “a 100% recall result guarantees no missed real-world floods.”

**Next gate:** reproduce stage-level confusion matrices from the underlying permitted geometries and frozen scores, then evaluate against independently observed local flood extents.
