# Literature-to-implementation critical matrix — GeoAlerta Sul

**Date:** 2026-10-26. **Scope:** selective critical synthesis, **not** a systematic review. Scientific claims are kept separate from source-code observations. The only GeoAlerta implementation considered is private `gilliardleda2018/geoalerta-sul`, branch main, previously observed at commit `a7943f49bd8c3fea15870f8b551e06bb92b61824`. Do not copy source or private datasets into this public dossier.

## Critical comparison matrix

| Literature / source | Method and scope supported by source | Limitation or open question for our study | GeoAlerta Sul evidence to inspect | Research experiment / measurable outcome |
|---|---|---|---|---|
| Mantovani et al. (2024), DOI 10.1038/s44304-024-00009-8 | Taquari-Antas September 2023 precipitation, 2D HEC-RAS flood simulation, remote sensing/DEM and flood risk mapping | Event-specific hydraulic mapping is distinct from real-time sensor-fusion and operational forecasting; do not claim paper shows lack of other platforms | `app/terrain.py`, `app/digital_twin.py`, `app/forecasting.py`, `docs/DATA_SOURCES.md` | Compare predicted levels and mapped exposure to independent observations, report spatial/temporal validity and uncertainties |
| Soares & Marcuzzo (2024), SGB repository 24993 | Preliminary 2023–2024 flooded-area study for Encantado and Muçum | Different municipalities and mapping methodology; not validation of Lajeado/Estrela predictions | `app/sar.py`, `app/official_exposure.py`, `app/terrain.py` | Geospatial intersection agreement and error stratified by city/event |
| Borges et al., DOI 10.5902/2236499483744 | Hydrological disaster processes in Estrela and Lajeado | Need to connect historical process explanation to predictive, temporally causal features | `app/hydrological_onsets.py`, `app/basin_forcing.py`, `app/features.py` | Ablate upstream hydrology, rainfall and topographic features with leave-one-event-out |
| Wilkinson et al. (2016), DOI 10.1038/sdata.2016.18 | FAIR principles for data stewardship and machine-actionable reuse | FAIR is not proof of accuracy or interoperability achieved by this project | `app/data_catalog.py`, `app/evidence.py`, `app/features.py` | Audit completeness of source IDs, timestamps, licensing, datum, quality, hashes and reproducible transformations |
| Idowu & Alfahid (2026), DOI 10.1016/j.envsoft.2025.106799 | Review on flood-model uncertainty quantification (bibliographic details to be reconfirmed before submission) | Need independently calibrated and tested predictive intervals and probability estimates | `app/ml_training.py`, `app/target_calibration.py`, `docs/ML_MODEL_CARD.md` | Coverage of 90% intervals by event, Brier, reliability curves, ECE and decision utility |
| GeoAlerta Sul internal model reports (not peer-reviewed) | Reported hybrid ExtraTrees and event classifier metrics, with event holdouts and safety gates | Reported metrics are not independently reproduced; 24h operational use blocked | `docs/EVENT_VALIDATION.md`, `docs/CRITICAL_EVENT_MODEL.md`, `models/evaluation/` | Freeze commit, dataset hash, run exact scripts, compare metrics by event, failures, calibration and source availability |

## Scientific gap: precise, falsifiable formulation

**Not defensible:** “Before GeoAlerta, there was no centralized flood information platform.” Existing SGB/ANA/Cemaden/Defesa Civil systems and scientific studies contradict an unqualified absence claim.

**Defensible hypothesis:** “Operational evidence relevant to municipal flood decisions is distributed across systems with different schemas, temporal freshness, vertical references, spatial granularity, data authority and licensing. A provenance-aware federated integration and decision layer may improve the completeness, interpretability and reliability of multi-source flood intelligence.” Test this claim empirically; distinguish centralizing access and evidence from taking ownership of official data.

## Proposed research questions

**RQ1 (integration):** Does source-aware normalization reduce invalid joins, stale-data acceptance and undocumented transformations compared with a naive aggregation baseline?

**RQ2 (prediction):** Do hybrid models outperform persistence and trend baselines under independent flood-event holdouts without reducing recall at operational thresholds?

**RQ3 (uncertainty):** Are predicted intervals and exceedance probabilities calibrated under missing sensors and shifts across flood events?

**RQ4 (transfer):** What configuration, retraining and data quality are required to port the architecture to another city or basin, and how does predictive performance change?

**RQ5 (decision):** Do provenance and uncertainty explanations improve a trained analyst's ability to identify unreliable predictions, without implying authorization for official warnings?

## Proposed experimental design and acceptance evidence

1. Freeze dataset manifests and source versions; record station IDs, timezones, datums, sensor outages and licensing.
2. Split by **entire event** and by chronological forward holdout, not randomly by correlated observation.
3. Compare persistence, linear trend, Ridge, ExtraTrees, boosting and critical-event logistic regression under identical event folds.
4. Report MAE, RMSE, event-level recall/precision, false alarms per episode, warning lead time, Brier, PR-AUC, interval coverage, confidence intervals and event-specific errors.
5. Simulate late/missing sensors and vertical-datum conflicts; report fail-closed behavior and graceful degradation.
6. For portability, define a new municipal configuration, independent event labels and an out-of-basin holdout; record engineering effort and transfer metrics.
7. Keep Earth-2 weather as **context only** until prospective model-admission criteria are met; no claim of improved skill from its presence alone.

## Source list and verification state

- Mantovani et al., *npj Natural Hazards* 1, 9 (2024), DOI https://doi.org/10.1038/s44304-024-00009-8 — publisher page reviewed.
- Wilkinson et al., *Scientific Data* 3, 160018 (2016), DOI https://doi.org/10.1038/sdata.2016.18 — bibliographic source in existing dossier; reconfirm full metadata during final copyedit.
- Soares and Marcuzzo, SGB institutional record https://rigeo.sgb.gov.br/handle/doc/24993 — institutional record in prior bibliography; confirm exact scope against full text.
- Borges et al., DOI https://doi.org/10.5902/2236499483744 — verify complete bibliographic metadata before submission.
- Idowu and Alfahid, DOI https://doi.org/10.1016/j.envsoft.2025.106799 — verify author/title metadata before submission.
- GeoAlerta Sul private repository: `docs/ML_MODEL_CARD.md`, `docs/EVENT_VALIDATION.md`, `docs/CRITICAL_EVENT_MODEL.md`, `docs/DATA_SOURCES.md`, `app/ml_training.py`, `app/forecasting.py`. Internal, not peer-reviewed.

**Status:** Critical matrix v1 completed. Pending: systematic database search protocol, full-text appraisal, citation metadata audit, runtime reproducibility, independent event testing and authorship evidence.
