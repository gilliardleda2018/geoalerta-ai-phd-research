# Machine Learning Portfolio — GeoAlerta Sul Only

**Status:** draft for applicant verification; **not** proof of individual authorship. All technical examples below come exclusively from the private `gilliardleda2018/geoalerta-sul` source. This file supersedes `initial-evidence-matrix.md` for submission scope; earlier SP/Tuntum references are historical drafts only.

## Example A — Hydrological forecasting
**Problem:** estimate river stage at Estrela 1–6 hours ahead. **Methods in source:** persistence, three-hour linear trend, Ridge, HistGradientBoosting and hybrid ExtraTrees residual correction. **Evidence:** `app/ml_training.py`, `scripts/evaluate_ana_events.py`, `models/evaluation/ana-events-loeo-v1.json`. **Reported results:** event-wise metrics and model comparisons, with post-hoc test-event winner-selection bias. **Applicant's exact role:** [TO BE CONFIRMED: authored modules, data engineering, experiments, decisions, code commits]. **Status:** source observed, authorship and independent rerun pending.

## Example B — Critical event prediction and safety gates
**Problem:** estimate exceedance of local Estrela 19m threshold while controlling false alarms. **Methods:** logistic classification, nested calibration, event-level holdouts, negative-period audits, shadow challenger and explicit non-promotion. **Evidence:** `app/event_model.py`, `app/target_calibration.py`, `models/evaluation/critical-events-stage5-decision.json`. **Result:** v6 challenger rejected retrospectively for false alarms. **Applicant's exact role:** [TO BE CONFIRMED]. **Status:** documented engineering and retrospective evaluation, not prospective validation.

## Example C — Geospatial susceptibility and exposure
**Problem:** spatially prioritize flood susceptibility and exposure. **Methods:** DEM/HAND, slope, drainage distance, ExtraTrees spatial AOI holdout, IBGE aggregated exposure and facility overlays. **Evidence:** `app/terrain.py`, `scripts/calibrate_topography_emsr720.py`, `app/exposure.py`, `models/evaluation/topography-emsr720-loao.json`. **Result:** baseline mean ROC-AUC 0.8444; enriched 0.8464 and not promoted. **Applicant's exact role:** [TO BE CONFIRMED]. **Status:** geographic within-event retrospective validation only.

## Professional foundation — SETUT
The applicant reports having worked as a SETUT systems analyst during electronic ticketing implementation in Teresina, using Oracle PL/SQL for early ticketing data analysis and supporting network collection/synchronization from buses through garages to the central database. This is **author testimony**, not a 2011 ML experiment or independently verified employment record. Supporting employment dates and artifacts are requested.

## Required applicant evidence before submission
- Identify personally written or led GeoAlerta Sul modules, algorithms, experiments and architecture decisions.
- Link relevant commit SHAs/PRs or approved technical reports, and distinguish own work from collaborators and third-party components.
- Identify accessible datasets and permissions; never attach private code or confidential operational data without authorization.
- Provide the dissertation PDF privately and confirm academic metadata.
- Approve all first-person authorship statements before emailing the professor.

**Do not use this draft to claim personal authorship of all repository modules.**
