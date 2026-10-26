# GeoAlerta Sul — focused technical and scientific audit (2026-10-26)

**Exclusive case study:** `gilliardleda2018/geoalerta-sul` (private). Previous SP/Tuntum preliminary evidence is superseded for this dossier. Audit is READ ONLY on source. Review covers repository tree (451 entries), README, DATA_SOURCES, ML_MODEL_CARD, SENSOR_FUSION, BACKTESTING, CRITICAL_EVENT_MODEL, operational-readiness, Earth-2 documentation and selected Python implementations. No independent execution or production validation.

## Real-world motivation: Vale do Taquari
Extreme floods in September 2023 and May 2024 exposed the need for integrated hydrological situational awareness. The SGB operates the Taquari hydrological warning system; Cemaden used ANA stations, COP-DEM, HEC-RAS 2D, HAND and Planet imagery for 2024 flood extent mapping. These are **external institutions' systems**, not GeoAlerta-developed methods. The region also has citizen-generated flood-mark datasets (UNIVATES/UFRGS). The existence of multiple sources supports an integration challenge; it does **not** prove that no other integrated platforms existed.

Sources: https://rigeo.sgb.gov.br/handle/doc/25002 ; https://www.gov.br/cemaden/pt-br/assuntos/monitoramento/notas-tecnicas/nota-tecnica-no-469-2024-sei-cemaden-mapeamento-das-manchas-de-inundacoes-e-enxurradas-do-desastre-ocorrido-no-rio-grande-do-sul-em-abril-maio-2024/ ; https://revistas.planejamento.rs.gov.br/index.php/boletim-geografico-rs/article/view/4616

## Centralization paradigm
The GeoAlerta Sul design aims to convert fragmented hydrometric, meteorological, geospatial, official risk, exposure and citizen evidence into a provenance-aware, quality-gated decision layer. Its catalogue defines source priority and explicit data quality, timestamp, datum, missingness and traceability contracts. It **federates and harmonizes evidence** rather than claiming to replace authoritative databases or official alert services.

## Technologies evidenced in repository
- Python, FastAPI-style API and Pydantic contracts; pandas, NumPy, scikit-learn and joblib for modeling and artifacts.
- Hydrological baseline: capped linear trend, 1–6 hour forecasts, uncertainty bounds and threshold probabilities.
- ML: hybrid ExtraTrees residual correction compared against persistence, linear trend, Ridge and HistGradientBoosting.
- Inputs: SGB/SACE, ANA, Defesa Civil RS, INMET/Cemaden, GOES-19, ECMWF ensemble, Copernicus DEM/HAND, Sentinel-1, Atlas/SEDEC, IBGE and other contextual sources, subject to actual ingestion/availability gates.
- Geospatial layers: official SGB risk sectors, SEDUR/UNIVATES zoning, flood mapping, terrain and population/infrastructure exposure, plus an optional 3D digital twin.
- AWS IaC, collectors, S3/DynamoDB/SQS/Athena/Glue artifacts and Earth-2 integration code. Presence of infrastructure definitions is not proof of active deployment.
- Earth-2 weather is documented as context-only and unweighted in the primary hydrological model until validation gates pass.

## Repository-reported evaluation (not independently reproduced)
Model card 2026.07-v3: 2,817 quarter-hour origins in July 2026; 27 SGB/SACE series; 189 candidate and 138 admitted features; hybrid ExtraTrees reported RMSE 0.0623m at 1h and 0.3336m at 6h in a temporal holdout. Single-event correlation limits generalization.
Later event holdout in model card: four 2023–2025 flood events, mean RMSE 0.0804m at 1h and 0.6878m at 6h, with minimum flood recall 0.8588 at 6h. Not promoted as operational artifact.
README reports separate Atlas severe-impact conditional model temporal holdout ROC-AUC 0.7070 and Brier 0.1752, with precision 54.5% and recall 51.6%; it does not predict flood occurrence. This is research-grade and not operationally sufficient.

## Safety and limitations
- The README explicitly states NOT an official alert system; evacuation requires confirmation by competent authorities.
- Lajeado/Estrela is the operational pilot; RS/SC/PR is declared geographic scope, but other areas are not validated. Do not claim universal deployment.
- Portability is **architectural potential**, not experimentally demonstrated transfer to any city. Replication requires local station mapping, vertical datums, thresholds, hydrological event history, data permissions, retraining, spatial overlays and validation.
- Source documents indicate future work, shadow/fail-closed gates, blocked 24h decision horizon and unpromoted Earth-2 inputs.
- No author-level attribution is inferred solely from repository access.

## Next verification tasks
Inspect full dataset manifests, event-level confusion matrices, calibration, failure cases, CI outcomes, provenance hashes, production status, commit authorship, source licenses, municipality-specific configuration and out-of-basin validation. Reproduce metrics in isolated environment before external scientific claims.
