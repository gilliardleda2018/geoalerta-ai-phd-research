# GeoAlerta Sul — Taquari-focused doctoral framing (draft)

**Working title:** Portable, Provenance-Aware and Uncertainty-Calibrated Geospatial AI for Flood Decision Support: The Vale do Taquari Case Study

## Scientific problem
Flood decision support requires integrating asynchronous and heterogeneous sources with different authority, latency, spatial coverage, sensor datum, and error properties. The 2023–2024 Vale do Taquari floods provide a consequential case study. GeoAlerta Sul attempts to centralize access to *interpretable evidence*, without claiming exclusive ownership of upstream datasets or replacement of official warnings.

## Research question
Can a portable, provenance-aware architecture combining quality-gated sensor fusion, hydrological forecasting and geospatial exposure models produce calibrated, transferable and operationally interpretable flood-risk estimates across municipalities?

## Hypotheses — not validated results
H1: Provenance/quality gates improve reliability under missing and stale feeds compared with naive aggregation.
H2: Hybrid statistical/ML models improve multi-event forecasts against persistence and linear trend baselines without compromising event-level flood recall.
H3: Transfer to new municipalities is feasible through explicit adapters, datums, thresholds, topography and recalibration; performance must be tested on held-out municipalities/basins.
H4: Uncertainty calibration and human-in-the-loop evidence improve decision utility under alert budgets.

## Experimental program
1. Freeze GeoAlerta Sul code and provenance; establish legally usable dataset manifests and event chronology.
2. Evaluate against SGB/SACE and other official event records with leave-one-event-out and forward-time holdouts; report MAE/RMSE, lead time, recall, false alarms, Brier/reliability and confidence coverage.
3. Ablate data families and source-quality gates; test sensor outages, vertical datum mismatch, stale telemetry and missingness.
4. Compare baseline, ExtraTrees, Ridge, boosting and hydrological methods with controlled tuning and external baselines.
5. Replicate in a second municipality/basin with independently validated thresholds and event data; measure transfer cost and performance, not just ability to configure.
6. Evaluate decision-support usability with competent authorities; no autonomous evacuation or official warning claims.

## Contribution sought
A generalizable evidence integration and evaluation methodology for multi-source geospatial flood intelligence, with demonstrable transfer criteria, robust uncertainty and explainability. Novelty remains subject to peer-reviewed literature review and experiments.

## Sources
GeoAlerta Sul private repository README and technical docs (inspected 2026-10-26); SGB Taquari SAH 2024 https://rigeo.sgb.gov.br/handle/doc/25002 ; Cemaden technical note https://www.gov.br/cemaden/pt-br/assuntos/monitoramento/notas-tecnicas/nota-tecnica-no-469-2024-sei-cemaden-mapeamento-das-manchas-de-inundacoes-e-enxurradas-do-desastre-ocorrido-no-rio-grande-do-sul-em-abril-maio-2024/ .
