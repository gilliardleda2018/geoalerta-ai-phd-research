# Traceability matrix — GeoAlerta Sul indicators, datasets, models, endpoints and evidence

**Audit date:** 2026-10-26. **Source:** private `gilliardleda2018/geoalerta-sul` branch `main`, reference SHA `a7943f49bd8c3fea15870f8b551e06bb92b61824` (verify file revisions before final submission). **Method:** direct static inspection of code and evaluation JSON; no independent model rerun, no live API request and no source redistribution. Only aggregate metrics and institutional entry points are published here.

## End-to-end scientific traceability

| Indicator family | Original source | Ingestion/transformation | API/consumer | Model/evaluation | Evidence level |
|---|---|---|---|---|---|
| River level, lag, trend and variability | SGB/SACE Taquari; historical ANA | `app/ml_dataset.py`, `app/evidence.py`, `app/features.py` | `GET /api/v1/indicators/latest`, `GET /api/v1/ml/forecast/latest`, `POST /api/v1/forecast` | `app/ml_training.py`; `docs/ML_MODEL_CARD.md`; `models/evaluation/ana-events-loeo-v1.json` | Retrospective temporal and leave-one-event-out, independent rerun pending |
| Rainfall accumulation and network coverage | SGB/SACE; ANA; DCRS, other context feeds | `app/features.py`, `app/basin_forcing.py`, `app/data_catalog.py` | `POST /api/v1/features/assemble`, `GET /api/v1/sensors/dcrs/latest` | Critical-event models `app/event_model.py`, reports v5 | Coverage gates documented; aggregation-window semantic tests pending |
| Critical-stage crossing probability | Gauge Estrela and multi-station history | `app/event_model.py`, calibration policy | `GET /api/v1/events/critical/latest`, `GET /api/v1/model/critical/validation` | LOEO, non-event 2021–2025 and v5/v6 shadow; `models/evaluation/critical-events-stage5-decision.json` | Retrospective evaluation; v6 rejected; 24h blocked |
| Topography, HAND, slope, drainage, TPI | Copernicus DEM/HAND, hydrography, EMSR720 | `app/terrain.py`; `scripts/calibrate_topography_emsr720.py` | `POST /api/v1/terrain/risk-points`, `GET /api/v1/map/risk` | `models/evaluation/topography-emsr720-loao.json` | Three-area spatial holdout; enriched model not promoted |
| Municipal flood-stage conformance | Lajeado municipal simulated stage maps | `scripts/evaluate_municipal_stages.py` | `GET /api/v1/map/municipal-stage`, `GET /api/v1/impact/municipal-stage` | `models/evaluation/lajeado-municipal-stages.json` | Conformance only, not independently observed flood extent |
| Population and facility exposure | IBGE aggregate grid, CNES, OSM, municipal facilities | `app/exposure.py`, `app/official_exposure.py` | `GET /api/v1/exposure/priorities`, `GET /api/v1/map/population-risk` | Rule-based physical-risk × exposure multiplier | Heuristic priority; no independent casualty/impact accuracy |
| Satellite and weather context | GOES-19, ECMWF, Copernicus, Earth-2 candidate | `app/goes_collector.py`, `app/earth2_weather.py` | `GET /api/v1/rain-proxy/latest`, `GET /api/v1/climate/earth2/latest` | Prospective admission gates in `docs/EARTH2_INTEGRATION.md` | Context only, no demonstrated core forecast skill improvement |

**Important:** mapping a source to a module is a traceability candidate, not evidence that every source is live or that every endpoint consumes the same source at runtime. Verify concrete call paths and snapshot manifests in the next phase.

## Audited implementation of the indicator registry

`app/indicators.py` defines `HydrometeorologicalIndicator` with explicit `name`, `value`, `unit`, `family`, `station_id`, `available_latest`, `train_coverage`, `admitted_to_model`, `admission_reason`, `uses_only_present_or_past`. `build_indicator_snapshot` constructs supervised features, matches feature decisions from a training report, represents non-finite latest values as null, obtains model predictions, and exposes a spatial-indicator name registry and source availability. This is an inspectable **implementation of indicator transparency**, not proof that a live endpoint has fresh data or a correct forecast.

## Spatial validation protocol inspected in source

`scripts/calibrate_topography_emsr720.py` reads EMSR720 observed-event geometries, samples a 90 m grid, labels points by geometric intersection, subsamples comparison negatives with seeded RNG 720 (three negatives per positive when available), extracts terrain features and fits `ExtraTreesClassifier(n_estimators=400, min_samples_leaf=5, max_features="sqrt", class_weight="balanced", random_state=720)`. Each held-out AOI is excluded from training; ROC-AUC, average precision and balanced accuracy at threshold 0.5 are recorded. Promotion requires mean ROC-AUC improvement >=0.02 and no loss in any held-out AOI.

**New methodological qualification:** negative subsampling deliberately creates an approximately 25% positive test prevalence. Average precision and threshold-specific predictive values depend on prevalence and may not represent actual geographic flood prevalence. ROC-AUC is less directly prevalence-sensitive but still subject to spatial sampling, label and event biases. Evaluate against full-area distributions and independent events before operational interpretation.

**Spatial dependence caveat:** AOI holdout is stronger than random-point holdout, but all three AOIs come from the same May 2024 event within one basin. This does not demonstrate generalization to another event, river basin or city nationwide.

## Public source links (not redistributable dataset guarantees)

- SGB/SACE https://www.sgb.gov.br/sace/
- ANA HidroWebservice https://www.ana.gov.br/hidrowebservice/swagger-ui/index.html
- Defesa Civil RS https://redehidrometeorologica.defesacivil.rs.gov.br/Mapa
- Copernicus Emergency Management https://rapidmapping.emergency.copernicus.eu/
- IBGE https://www.ibge.gov.br/
- CNES https://cnes.datasus.gov.br/
- SEDEC/S2ID https://s2id.mi.gov.br/paginas/series/

Do not publish raw private telemetry archives, restricted historical API payloads, proprietary source code, cloud storage locations, or personal data. Verify each dataset's actual license, provider release version and DOI/permalink when available.

## Reproduction tasks and acceptance evidence

1. Freeze source commit, all data/manifest hashes, model version and environment.
2. Derive actual endpoint-to-function call graph, with request/response contract and failure behavior.
3. Audit temporal semantics of rainfall accumulation and cross-gauge vertical datum explicitly.
4. Recreate EMSR720 sampling and holdouts from permitted source data; verify 2,016 samples, label balance, AUC/AP and per-AOI gates.
5. Evaluate class prevalence sensitivity using full-AOI comparison negatives and event-specific uncertainty intervals.
6. Recompute indicators and model results per event, including false alarm episodes and reliability plots.
7. Verify independent 2023–2025 event and prospective coverage, avoiding unqualified use of the word 'validated'.
8. Compare portability through an additional basin/municipality with independent station mappings, datums, thresholds, data permissions and holdout outcomes.

## Scientific framing

The thesis is not that centralization alone guarantees superior predictions, but that a **provenance-aware, physically consistent, uncertainty-calibrated and portable decision-support architecture** can be evaluated against transparent baselines under missing and heterogeneous evidence. Originality remains an empirical and literature-review question.

References: WMO (2025), *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364, https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts ; Wilkinson et al. (2016), *Scientific Data*, https://doi.org/10.1038/sdata.2016.18 .
