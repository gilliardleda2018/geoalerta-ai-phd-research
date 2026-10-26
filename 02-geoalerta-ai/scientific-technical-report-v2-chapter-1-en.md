# GeoAlerta Sul — Scientific and Technical Report
## Chapter 1: Architecture, Evidence Provenance and Validation Boundaries

**26 October 2026 | Academic manuscript v2 | Not submitted**  
**For:** Prof. Carlos Cano Domingo, UPC BarcelonaTech  
**Technical evidence:** private `gilliardleda2018/geoalerta-sul`, read-only code and report audits. Results are repository-reported unless explicitly marked arithmetic-recomputed.

### Abstract
GeoAlerta Sul is an experimental geospatial flood decision-support system developed around the Vale do Taquari. It combines hydrological observations, meteorological context, terrain models, satellite flood mapping and aggregated exposure data. This chapter explains its technical architecture and, critically, the conditions under which its outputs can be considered scientifically valid. It distinguishes source availability from source admissibility, code declarations from deployed services, retrospective scores from independent validation, and engineering portability from predictive transfer.

## 1. Research setting and public ground truth
The Copernicus Emergency Management Service activated flood mapping mission EMSR720 on 2 May 2024. Official products cover Encantado, Roca Sales and Santa Tereza, among other areas [E1,E2]. These constitute valuable observed-event spatial reference products, but their detection accuracy depends on satellite acquisition, urban/vegetation conditions and product versions. The operational hydrological pilot in GeoAlerta Sul is Lajeado/Estrela. Neither the EMSR720 experiment nor simulated Lajeado stage maps independently demonstrates nationwide model performance.

## 2. System architecture and implementation status
The inspected repository describes Python/FastAPI 0.141.1, Pydantic 2.13.4, Uvicorn, pandas, NumPy, scikit-learn 1.7.2 and joblib. Geospatial code uses rasterio, shapely, pyproj, scipy and terrain rasters. Core source modules include `app/main.py`, `app/maps_main.py`, `app/municipal_api.py`, `app/evidence.py`, `app/features.py`, `app/ml_training.py`, `app/ml_inference.py`, `app/event_model.py`, `app/terrain.py` and `app/exposure.py` [P1].

Six research layers must remain conceptually separate: **(1)** acquisition and institutional authority; **(2)** normalization/provenance; **(3)** physical and temporal feature construction; **(4)** ML and heuristic outputs; **(5)** model admission and human governance; **(6)** API and geographic decision interfaces. Source contains CloudFormation definitions for Lambda, S3, SQS, EventBridge, ECR, CloudWatch, IAM and AWS Batch/GPU context workers. DynamoDB/Glue/Athena/SageMaker appear in foundational or proposed documentation. Deployed state and activated security controls have not been independently attested. The source also contains historical Google Cloud integration references; do not call it an exclusively AWS deployment.

## 3. Institutional source registry and provenance
| Source | Scientific role | Critical condition | Public entry point |
|---|---|---|---|
| SGB/SACE | Primary river level and rainfall | Station datum, authority, timestamp | https://www.sgb.gov.br/sace/ |
| ANA | Historical hydrometric observations | Per-station gaps, source/version | https://www.ana.gov.br/hidrowebservice/swagger-ui/index.html |
| Defesa Civil RS | Independent monitoring context | Latency, authorized history | https://redehidrometeorologica.defesacivil.rs.gov.br/Mapa |
| Copernicus EMSR720 | Flood delineation labels | Acquisition time, spatial uncertainty | https://rapidmapping.emergency.copernicus.eu/EMSR720/ |
| IBGE | Aggregated population | Grid coverage, no personal inference | https://www.ibge.gov.br/ |
| CNES | Registered health facilities | Registry ≠ live service capacity | https://cnes.datasus.gov.br/ |
| S2ID/SEDEC | Disaster history and impacts | Post-event outcome leakage | https://s2id.mi.gov.br/paginas/series/ |

The ANA/SGB manifests document upstream URLs, source and normalized-file hashes, record counts and event windows [P2]. One ANA event manifest records **zero** observations for `linha_colombo` in September 2023. Therefore inclusion in a source catalog does not imply coverage in an experiment. Dataset SHA-256 values are manifest-reported, not verified against raw bytes.

Three concrete semantic tests follow from the source audit [P3]: overlapping rainfall accumulation windows must not be naively summed; river-level gauges with different vertical datums must not be averaged without harmonization; and excluding future observations is not equivalent to imposing a maximum source age. These are function-level review findings, not confirmed production incidents.

## 4. API evidence and contract boundaries
The static route audit [P4] inventories the following families:
- **Health and provenance:** `GET /health`, `GET /ready`, `GET /api/v1/data-sources`.
- **Normalization and features:** `POST /api/v1/evidence/normalize`, `POST /api/v1/features/assemble`.
- **Hydrological forecasts:** `GET /api/v1/ml/forecast/latest`, `POST /api/v1/forecast`.
- **Critical events:** `GET /api/v1/events/critical/latest`, `GET /api/v1/model/critical/validation`.
- **Terrain and mapping:** `GET /api/v1/map/risk`, `POST /api/v1/terrain/risk-points`, `GET /api/v1/map/copernicus/emsr720`.
- **Exposure:** `GET /api/v1/exposure/priorities`, `GET /api/v1/map/population-risk`.
- **Municipal governance:** `GET /api/v1/municipal/{municipality}/decision`.
- **Context:** `GET /api/v1/climate/earth2/latest`, `GET /api/v1/sensors/ana/latest`.

The route inventory has a counting ambiguity between its summary and enumerated declarations, so this manuscript makes **no unsupported total endpoint claim**. Declared routes are not verified HTTP responses. Some `latest` endpoints can legitimately be unavailable when data is stale. `/health` does not establish `/ready`; `/api/v1/evacuation/candidate-route` is not an approved safe evacuation route. The security wrapper includes feature flags whose live activation is unverified.

## 5. Distinct models and validation targets
**River-stage regression:** hybrid ExtraTrees residual correction, Ridge, HistGradientBoosting and transparent persistence/trend baselines. The July 2026 cohort has 189 candidate indicators, 147 available and 138 admitted; a different four-event historical cohort has 238 candidates. They are not the same training population.

**Critical threshold classifier:** logistic regression and calibration target crossing the local Estrela 19 m gauge threshold at specified horizons. ROC-AUC, average precision, Brier, recall and false-alarm episodes measure different properties; discrimination does not prove calibration.

**Terrain susceptibility:** HAND, drainage distance and slope are combined in a deterministic weighted score (0.55, 0.30 and 0.15). Its value is not a calibrated flood probability. A separate ExtraTrees classifier is evaluated against Copernicus observed extents.

**Exposure ranking:** aggregated population and facilities modify priorities; this is not a prediction of casualties, damage or safe routes.

**Earth-2/AIFS:** meteorological ensemble context only, not scientifically admitted into the core flood forecast. **LLM copilots:** decision-oriented interfaces do not prove a conversational LLM or validated agentic system [P1].

## 6. Event-wise river-level forecasting: arithmetic and bias
The retrospective benchmark covers September/November 2023, May 2024 and June 2025. Code trains each candidate without the held-out event, but then selects the lowest-RMSE winner using the held-out event itself. This creates **test-set model-selection optimism** [P5]. A secondary analysis compared a fixed ExtraTrees candidate with persistence across all four stored event rows; these are event-macro means, not new inference [P6].

| Horizon | Persistence RMSE (m) | Fixed ExtraTrees RMSE (m) | Descriptive reduction |
|---|---:|---:|---:|
| 1 h | 0.283325 | 0.081700 | 71.16% |
| 2 h | 0.564450 | 0.175900 | 68.84% |
| 3 h | 0.844625 | 0.293500 | 65.25% |
| 4 h | 1.123725 | 0.408950 | 63.61% |
| 5 h | 1.401925 | 0.537525 | 61.66% |
| 6 h | 1.679750 | 0.697200 | 58.49% |

The candidate itself was selected retrospectively for this secondary analysis, so this is **not** a preregistered fixed-model trial. A second concern is numerical provenance: the narrative report gives selected-model mean RMSE 0.0804 m (1h) and 0.6878 m (6h), while direct arithmetic from the serialized JSON yields 0.078525 m and 0.677475 m. Differences occur at all horizons and weighted pooling does not reconcile them [P7]. The origin remains unknown.

**Required experiment:** nested leave-one-event-out selection: select features, preprocessing, model and hyperparameters using only the three outer-training events; refit; evaluate once on the untouched fourth event. Report each event and acknowledge that four independent floods provide limited inferential power.

## 7. Critical-event calibration and safety
| Horizon | Raw Brier | Calibrated Brier | Finding |
|---|---:|---:|---|
| 6 h | 0.0841 | 0.0740 | Improvement |
| 12 h | 0.0910 | 0.1148 | Calibration worsened |
| 24 h | 0.2939 | 0.1989 | Still blocked for false positives |

The 24h model reports a 64.55% false-positive fraction among negative origins. The v6 shadow challenger was rejected at 6h and 12h despite satisfying a recall threshold because false-alarm/persistent-signal gates failed [P8]. This is a governance result, not proof of prospective safety. The non-event evaluator computes `signal_hours` from positive-origin counts ×0.25 h; irregular observation gaps can make this differ from elapsed wall-clock duration [P2].

## 8. Copernicus spatial holdout and municipal conformance
The EMSR720 experiment reports 2,016 sampled points (504 positives, 1,512 comparison negatives), yielding a constructed 25% positive prevalence. Three held-out AOIs all belong to the same May 2024 event and basin.

| Held-out area | Baseline ROC-AUC | Enriched ROC-AUC | Delta |
|---|---:|---:|---:|
| Encantado | 0.8491 | 0.8666 | +0.0175 |
| Roca Sales | 0.7657 | 0.7489 | −0.0168 |
| Santa Tereza | 0.9184 | 0.9237 | +0.0053 |
| Mean | 0.8444 | 0.8464 | +0.0020 |

Enrichment fails the >=0.02 mean improvement and no-area-degradation promotion policy. Negative samples outside the delineated area are **comparison negatives**, not guaranteed dry land. Precision/AP depend on sample prevalence.

Lajeado's 18 municipal **simulated-stage** polygons cover 11,235 evaluation points, with mean static ROC-AUC 0.8007. At simulated 21 m, the risk rule has 100% recall but only 1.92% precision against the simulated reference. The best classification threshold is selected using the same evaluated labels [P9]; it is not an independent generalization estimate. Stages 31 m and 33 m require geometry review.

## 9. Experimental assurance and release gates
| Gate | Status | Evidence needed |
|---|---|---|
| Code and schema inventory | Source inspected | Immutable full source revision |
| Dataset integrity | Hashes in manifests | Compare with original permitted bytes |
| Regression evaluation | Stored results + arithmetic audit | Nested model rerun and event logs |
| Critical calibration | Stored retrospective report | Independent reliability plots and negative windows |
| Spatial validation | Same-event AOI holdout | New observed event and full-area sampling |
| Municipal conformance | Simulated-stage benchmark | Independent observed local flood footprint |
| Portability | Architectural design | Second-basin measured adaptation and skill |
| API security/runtime | Declared source | Authorized runtime contract and auth tests |

No automatic public warning, evacuation or safety certification is claimed. Publication must exclude confidential source, credentials, licensed raw data and personally identifying exposure records.

## 10. Research contribution and limitations
The strongest doctoral question is not whether one model has a high ROC-AUC. It is whether physically consistent, provenance-aware multi-source evidence and explicit uncertainty/governance controls measurably improve reliability under event shift, missing sensors and geographic transfer. The proposed contributions are reproducible data-quality benchmarks, nested event-wise model verification, calibrated decision gates, spatial-label uncertainty analysis and an empirical cross-city transfer protocol. Their novelty and effect sizes remain to be demonstrated.

### Public evidence
[E1] Copernicus EMS, *Flood in Rio Grande do Sul State, Brazil*, 3 May 2024: https://mapping.emergency.copernicus.eu/news/flood-in-rio-grande-do-sul-state-brazil/  
[E2] Copernicus EMSR720 production story and download links: https://storymaps.arcgis.com/stories/0bfb6520fbb9485e8b5acc5900b44787  
[E3] WMO, *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364 (2025).  
[E4] Wilkinson et al. (2016), FAIR Principles, https://doi.org/10.1038/sdata.2016.18.

### Internal technical audit references (not peer reviewed)
[P1] `architecture-ai-infrastructure-audit.md`; [P2] `data-lineage-calibration-false-alarms-audit.md`; [P3] `evidence-feature-semantics-audit.md`; [P4] `api-endpoint-audit.md`; [P5] `loeo-model-selection-bias-critical-audit.md`; [P6] `fixed-extratrees-event-recalculation-and-reconciliation.md`; [P7] `full-six-horizon-report-reconciliation.md` and `pooled-versus-macro-rmse-reconciliation.md`; [P8] `critical-model-artifact-audit-v5-v6.md`; [P9] `municipal-stage-threshold-validity-audit.md`. All under `02-geoalerta-ai/` of this academic repository; primary technical source is private GeoAlerta Sul.
