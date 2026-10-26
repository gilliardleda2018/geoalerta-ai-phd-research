# GeoAlerta Sul — Technology, AI, copilots and infrastructure audit

**Source:** private `gilliardleda2018/geoalerta-sul` branch main, read-only static review on 2026-10-26. The source repository is the **only** technical GeoAlerta implementation considered for the UPC dossier. No private code or secrets reproduced.

## 1. Architecture and technology inventory

- **Application:** Python, FastAPI 0.141.1, Pydantic 2.13.4, Uvicorn; `app/main.py` core; `app/maps_main.py` maps-only application; `app/municipal_api.py` internal decision API. Browser interfaces use HTML/CSS/JavaScript; landing is a separate Next.js/TypeScript application. 
- **Analytics and ML:** NumPy, pandas, scikit-learn 1.7.2, joblib; ExtraTreesRegressor, Ridge, HistGradientBoostingRegressor, logistic regression, ensemble comparisons, imputation, permutation importance and temporal/event holdouts.
- **Geospatial:** rasterio, shapely, pyproj, pyshp, scipy; terrain rasters, HAND, Copernicus DEM GLO-30, risk sectors, zoning, flood extents, exposure overlays and optional symbolic-height 3D digital twin. Map frontend documentation identifies Leaflet, OpenStreetMap/CARTO, MapLibre and terrain services. Do not call the twin a validated 2D hydraulic simulation.
- **Sensor/earth observation:** SGB/SACE (operational primary), ANA, DCRS, INMET/Cemaden, GOES-19 rainfall proxy, ECMWF ensemble, Sentinel-1 SAR, Copernicus EMS, SEDEC/Atlas, SGB official risk mapping, SEDUR/UNIVATES, IBGE and other exposure layers. Sources have different authority, latency and admissibility. Source catalog is an integration contract, not proof all feeds are currently live.
- **AWS:** CloudFormation templates for IAM bootstrap, Lambda API, collectors, AWS Batch GPU Earth-2 worker, S3, SQS/DLQ, EventBridge scheduling, ECR, CloudWatch, plus DynamoDB/Glue/Athena foundation resources. The historical-data platform document proposes S3 bronze/silver/gold, Glue/Athena, DynamoDB hot state and SageMaker; distinguish proposed architecture from verified deployments. Google Cloud Storage dependency and cloudbuild manifests also exist; do not assert AWS-only historical operation.
- **Security:** HTTPS conditional middleware, security headers/CSP, rate limiting, payload limits, Secrets Manager and anomaly monitoring code. Flags default off in `app/main_secured.py`; deployed activation not established. Security implementation is not equivalent to a penetration test.

## 2. Models and empirical evidence

| Model | Purpose | Repository-reported evidence | Limitation |
|---|---|---|---|
| Linear trend baseline | Estrela river level, 1–6 h | `docs/BACKTESTING.md`: July 2026 700 hourly origins, RMSE 0.0937m at 1h, 0.6186m at 6h | One correlated hydrological episode |
| Hybrid ExtraTrees residual model | River level 1–6 h | `docs/ML_MODEL_CARD.md`: July temporal holdout RMSE 0.0623m at 1h, 0.3336m at 6h | Same-event generalization; not multi-event validated in this artifact |
| Event-holdout model comparison | Cross-event level prediction | `docs/EVENT_VALIDATION.md`: four events 2023–2025, mean RMSE 0.0804m at 1h, 0.6878m at 6h; worst 6h recall 0.8588 | Single basin, four events; not approved for autonomous alert |
| Critical-event classifier | Probability Estrela crosses 19m in 6/12/24h | `docs/CRITICAL_EVENT_MODEL.md`: logistic regression selected; 6h ROC-AUC 0.9680, AP 0.8637, Brier 0.0841; 12h ROC-AUC 0.9765, AP 0.8321, Brier 0.0910 | Retrospective, correlated observations; 24h blocked |
| Atlas severe-impact model | Conditional severity of documented events | README temporal holdout ROC-AUC 0.7070, Brier 0.1752, precision 54.5%, recall 51.6% | Not a flood occurrence predictor; insufficient for operational decisions |
| Earth-2 / AIFS ENS 2.0 | Meteorological precipitation context | `docs/EARTH2_INTEGRATION.md`: Earth2Studio 0.18.0, 5 ensemble members, 12 six-hour steps, 0.25-degree bounding box, `tp06` | Release candidate; context-only, not prospectively validated or promoted |

**Important:** These numbers are extracted from repository reports, not rerun or independently reproduced. Inspect model artifacts, training data, timestamps, event labels, data leakage, confidence calibration and source revisions before scholarly submission.

## 3. AI copilots — explicit distinction

The repository includes decision-oriented UI, geospatial point evidence, event-risk explanations, operational priority ranking and an internal municipal decision snapshot requiring human review. These support a **decision-support assistant experience** in a broad product sense. Static inspection of named routes and dependencies did **not** establish a conversational LLM copilot, agentic tool execution, retrieval-augmented generation, prompt/version evaluation, model-provider integration or independent copilot performance metrics. Do not claim such capabilities as implemented without locating and testing exact code, endpoints, prompts, providers, authorization and evaluation datasets.

`app/ai_security.py` contains configurable pattern-based request anomaly detection with baseline thresholds; its existence does not establish a trained foundation model or a security copilot. `ENABLE_AI_SECURITY` defaults false in inspected code.

## 4. Portability hypothesis

The design separates geographic configuration, source adapters, models, maps and exposure layers. This is an **architectural portability claim**, not a demonstrated guarantee that the trained Estrela model predicts accurately in any city. Replication requires local sensors, station codes, flood thresholds, vertical datums, hydrological topology, maps, governance and independent event validation. Lajeado/Estrela is the pilot; RS/SC/PR is intended product scope, not validated city-by-city deployment.

## 5. Engineering and science gaps

- Confirm the exact deployed image digests, Lambda/ECS/Batch job states, collector freshness and security flags using read-only AWS evidence.
- Export runtime OpenAPI, run contract tests and produce response/failure examples for each API route.
- Identify source-specific data licenses, timezones, vertical references, missingness, backfill and sensor outages.
- Reproduce holdout metrics with frozen data/model hashes; report confidence intervals and event-level false negatives.
- Verify cross-basin transfer, calibration and risk-decision utility before claiming generalization.
- Identify personally authored modules using commits, technical reports and interviews; repository ownership alone is not proof of individual implementation.
