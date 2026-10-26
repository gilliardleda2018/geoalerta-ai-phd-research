# GeoAlerta Sul — Indicator Validation Dossier (v1)

**Date:** 2026-10-26. **Source:** private `gilliardleda2018/geoalerta-sul` branch main, read-only static inspection. **Status:** repository-reported evaluations, not independently reproduced. Do not distribute private data, proprietary models or source-code extracts. This public document includes only aggregate metrics and official source links.

## 1. Indicator taxonomy and admission

`docs/INDICATORS.md` reports **189 candidate hydrometeorological indicators**, 147 currently available (77.78%), and 138 admitted to a trained model (73.02% of candidates; 93.88% of available). Rejections: 46 insufficient training coverage and 5 constant features (51 rejected = 26.98% of candidates). Availability and admission are distinct; these counts are from the documented snapshot, not independently queried from live API. There are also **30 spatial indicators per point**; these are descriptive territorial evidence, not all independently validated predictors. Exact station/time coverage and feature admission must be checked against model artifact.

## 2. Validated versus evaluated versus not promoted

| Family / algorithm | Evaluation reported | Quantitative result | Scientific and operational qualification |
|---|---|---|---|
| River-stage hybrid ExtraTrees residual regression | July 2026 temporal holdout; separate four-event leave-one-event-out | 1h RMSE 0.0623m (July); 6h RMSE 0.3336m (July); multi-event 1h 0.0804m and 6h 0.6878m | Retrospective, single basin, independent replication pending; no autonomous warning |
| Critical threshold crossing, logistic regression | Four flood events LOEO, July 2026 retrospective holdout | LOEO 6h ROC-AUC 0.9680, AP 0.8637, Brier 0.0841 | 24h not operationally admissible; event-level sensitivity varies |
| Calibrated critical event v2 | Nested event-based sigmoid calibration | 6h Brier 0.0841 -> 0.0740 (12.01% relative reduction); 12h 0.0910 -> 0.1148 (26.15% worsening); 24h 0.2939 -> 0.1989 but 64.55% negative-row false-positive fraction | Calibration does not imply operational approval; 24h blocked |
| Topographic ExtraTrees baseline | EMSR720 May 2024, leave-one-area-out across Encantado, Roca Sales, Santa Tereza; 2,016 points (504 positive, 1,512 comparison negatives) | Mean ROC-AUC 0.8444; mean AP 0.6065 | Upstream AOIs only; comparison negatives are not proof of no flood |
| Enriched topographic ExtraTrees | Same held-out AOIs, 8 features | Mean ROC-AUC 0.8464 vs 0.8444 (+0.0020 absolute); Roca Sales 0.7489 vs 0.7657 (-0.0168) | **Not promoted**: requires >=0.02 gain and no loss in any held-out AOI |
| Static municipal terrain conformance | 18 Lajeado municipal stage maps; 11,235 grid points | Mean ROC-AUC 0.8007 (0.7709–0.8427) | Conformance to simulated municipal maps, not independent observed-event validation |
| Exposure priority score | Rule-based physical-risk × exposure multiplier (1.0–1.5) | No independent impact-ranking accuracy established in inspected docs | Ranking is heuristic, not a calibrated probability or validated evacuation route |
| Earth-2 / AIFS ENS context | Worker integration and quality gates | No independently demonstrated hydrological skill gain | Context-only, not admitted to critical model |

## 3. Data-source provenance and non-sensitive public entry points

- SGB/SACE/SAH Taquari: https://www.sgb.gov.br/sace/ ; official operational river-stage and rainfall reference, with local gauge datums.
- ANA HidroWebservice: https://www.ana.gov.br/hidrowebservice/swagger-ui/index.html ; authenticated historical hydrometric records, subject to registration and service policy.
- Defesa Civil RS: https://redehidrometeorologica.defesacivil.rs.gov.br/Mapa ; independent operational context; historical GraphQL may require authorization.
- Cemaden: https://www.gov.br/cemaden/pt-br ; risk monitoring and event technical reports.
- Copernicus EMS: https://rapidmapping.emergency.copernicus.eu/ ; observed flood extent products, including EMSR720; inspect licensing and product geometry before redistribution.
- IBGE: https://www.ibge.gov.br/ ; aggregated population/household statistical grid, not person-level records.
- CNES: https://cnes.datasus.gov.br/ ; health facilities, not proof of current service capacity.
- SEDEC/S2ID: https://s2id.mi.gov.br/paginas/series/ ; disaster history and post-event impacts, **not live forecast inputs**.
- INMET: https://portal.inmet.gov.br/ ; weather observations/forecasts.
- ECMWF: https://www.ecmwf.int/ ; ensemble meteorology, context until independent admission.
- NASA GOES imagery/data access should be documented against the exact provider and product license in the ingestion manifest; no direct dataset URL asserted here without verification.

**Availability caveat:** these are institutional entry points, not verified live download URLs or unrestricted redistribution licenses. Do not publish internal S3 buckets, credentials, model artifacts, operational logs or non-public municipal datasets.

## 4. Quantitative charts to include in submission

**Chart A — Indicator admission:** 189 candidates, 147 available, 138 admitted, 51 rejected; use denominators explicitly. Note available and admitted are not mutually exclusive groups.

**Chart B — Topographic ROC-AUC:** baseline vs enriched: Encantado 0.8491 vs 0.8666; Roca Sales 0.7657 vs 0.7489; Santa Tereza 0.9184 vs 0.9237. Mean 0.8444 vs 0.8464. Do not present tiny mean gain as operational superiority.

**Chart C — Critical-event Brier before/after calibration:** 6h 0.0841 -> 0.0740, 12h 0.0910 -> 0.1148, 24h 0.2939 -> 0.1989; 24h blocked despite reduction.

**Chart D — Forecast RMSE 1h and 6h:** July temporal holdout 0.0623m/0.3336m versus multi-event LOEO 0.0804m/0.6878m. Different evaluation designs; comparison illustrates generalization risk, not an identical test cohort.

## 5. Scientific cautions

1. 'Validated' must be qualified: **retrospectively evaluated**, **spatially held-out**, **independently reproduced**, **prospectively validated** and **operationally approved** are different states.
2. Synthetic/heuristic indicators are not direct physical measurements. Exposure ranking is not an observed casualty or damage forecast.
3. Municipal flood-stage maps are simulated references; agreement is conformance, not independent validation against observed flooding.
4. The EMSR720 experiment does not cover Lajeado/Estrela directly. Cross-city portability remains a testable hypothesis, not a demonstrated universal model.
5. Publication requires raw-source hash verification, independent rerun, uncertainty intervals, event-specific failure analysis, licensing review and author approval.

## 6. Exact repository evidence

`docs/INDICATORS.md`; `docs/ML_MODEL_CARD.md`; `docs/EVENT_VALIDATION.md`; `docs/CRITICAL_EVENT_MODEL.md`; `docs/CRITICAL_EVENT_CALIBRATION_V2.md`; `docs/TOPOGRAPHY_CALIBRATION.md`; `docs/MUNICIPAL_STAGE_CONFORMANCE.md`; `docs/EXPOSURE_PRIORITY.md`; `models/evaluation/topography-emsr720-loao.json`; `app/indicators.py`; `app/target_calibration.py`. All are internal project evidence, not peer-reviewed publications.

## 7. Literature anchors

Wilkinson et al., *Scientific Data* 3:160018 (2016), DOI https://doi.org/10.1038/sdata.2016.18 (data provenance and stewardship). WMO, *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364 (2025), https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts (verification of forecast skill, reliability and uncertainty). These references motivate the methodology; they do not independently verify GeoAlerta metrics.
