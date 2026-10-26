# Doctoral Research Proposal — GeoAlerta Sul

**Working title:** Provenance-Aware, Uncertainty-Calibrated and Transferable Geospatial AI for Flood Decision Support: Evidence from the Vale do Taquari, Brazil

**Applicant:** Gilliard Cardoso Léda  
**Prospective academic discussion:** Prof. Carlos Cano Domingo, UPC BarcelonaTech  
**Status:** Research proposal draft, not submitted, no admission/funding commitment.  
**Date:** 26 October 2026

## Abstract

Flood intelligence depends on heterogeneous observations from hydrometric stations, meteorological systems, satellite products, terrain models and exposure registries. Their differing temporal resolution, spatial support, source authority, uncertainty and availability create a research challenge that is not resolved merely by displaying multiple feeds in one interface. GeoAlerta Sul is an experimental geospatial AI platform focused on the Vale do Taquari, where severe floods in 2023 and 2024 motivate systematic investigation. The proposed doctoral research asks whether provenance-aware integration, event-independent prediction, calibrated uncertainty and safety-constrained model governance can improve the reliability and interpretability of flood decision support, and what is required to transfer this architecture to other cities. The research will combine reproducible data engineering, statistical and ML baselines, spatial/temporal holdouts, uncertainty verification, controlled failure experiments and independent geographic transfer studies. It will not replace official hydrological monitoring or public-warning authority.

## 1. Problem and motivation

The scientific problem is the reconciliation of heterogeneous environmental evidence for temporally and geographically defensible risk estimates. Existing SGB, ANA, Cemaden and Civil Defense information systems provide essential authoritative observations and warnings. GeoAlerta Sul proposes an additional integration and decision-support layer, not a claim that no institutional monitoring or centralization previously existed. The September 2023 Taquari-Antas flood has been studied in peer-reviewed research [1]; the project must quantitatively test the costs of source fragmentation instead of asserting them without measurement.

## 2. Research question

**Under what conditions does provenance-aware, quality-gated fusion of hydrometeorological and geospatial observations, combined with calibrated predictive models and human-governed model admission, improve flood-risk decision support and remain transferable across municipalities?**

## 3. Hypotheses

**H1 — Source semantics:** explicit checks on provenance, timestamps, vertical datums, accumulation windows and missingness reduce invalid feature construction relative to naive integration.

**H2 — Event generalization:** hybrid statistical/ML river-stage models improve event-wise skill over persistence and trend baselines, but improvements vary with flood type, sensor coverage and forecast horizon.

**H3 — Uncertainty and safety:** event-based probability calibration and false-alarm-aware promotion improve decision reliability relative to ranking models by discrimination alone.

**H4 — Transferability:** source adapters and territorial configuration enable reuse across cities, but predictive skill requires local calibration and may degrade under hydrological domain shift.

**H5 — Human interpretation:** provenance and uncertainty displays improve expert identification of unreliable predictions in blinded decision-support tasks.

These are prospective hypotheses, not already established scientific contributions.

## 4. Existing implementation and preliminary evidence

The GeoAlerta Sul source contains Python APIs, Pydantic source contracts, hydrological features, ExtraTrees residual correction, Ridge and boosting comparisons, critical-event logistic models, DEM/HAND/drainage spatial analysis, exposure heuristics, satellite/weather context adapters, tests, model reports and infrastructure definitions. An event-holdout report [P1] describes four floods (September/November 2023, May 2024, June 2025) with mean 1h RMSE 0.0804m for the hybrid model versus 0.2833m persistence, and 6h RMSE 0.6878m versus 1.6798m. The 6h minimum reported flood recall is 0.8588. A separate May 2024 Copernicus EMSR720 spatial holdout [P2] yields baseline ROC-AUC 0.8444 and enriched 0.8464; enrichment fails promotion gates. These are repository-reported retrospective evaluations, not independent reproductions or prospective demonstrations.

## 5. Work packages and methods

**WP1 — Evidence provenance and source-quality benchmark.** Freeze source/data versions, map institutional data rights, document stations, timezones, observation latency, vertical datums and missingness. Compare source-aware normalization against naive joins. Measure invalid joins, false availability, stale acceptance and completeness.

**WP2 — Hydrological predictive verification.** Evaluate persistence, linear trend, Ridge, HistGradientBoosting and hybrid ExtraTrees on identical origins with whole-event and forward-time holdouts. Report per-event RMSE, MAE, bias, flood recall, precision, lead time and false-alarm episodes. Prevent leakage in feature selection and calibration.

**WP3 — Probabilistic reliability and governance.** Compare raw and calibrated probabilities using Brier score, reliability diagrams, calibration slope, event-wise coverage and false-alarm burden. Evaluate safety gates, including missing observations and threshold anomalies. No autonomous public warnings.

**WP4 — Spatial flood evidence.** Evaluate HAND, drainage, slope and enriched terrain indicators against Copernicus observed extents and independent local observations. Keep municipal simulated-stage conformance separate from event validation. Quantify spatial IoU, precision, recall, label uncertainty and prevalence sensitivity.

**WP5 — Geographic transfer and expert evaluation.** Configure a second municipality/basin with independently obtained data, thresholds and datums; quantify adapter effort and predictive degradation. Assess whether provenance and uncertainty explanations assist expert reviewers, without making unsupported claims about LLM copilots.

## 6. Evaluation design

Primary statistical units are flood events, municipalities and sensor outage episodes, not highly correlated 15-minute rows. Predefine data splits, baselines and model-selection policies before opening final holdouts. Use event-block uncertainty where sample size allows, report all event-level failures and avoid claiming significance from four events alone. Preserve negative results.

## 7. Expected contributions

A source-quality and provenance benchmark; reproducible event-level evaluation of hydrological ML; a tested calibration/model-governance protocol; a spatial-label uncertainty analysis; and measurable criteria for transferring geospatial flood intelligence across municipalities. The novelty of each contribution will be established through deeper literature review and experiments.

## 8. Ethics, licensing and limitations

GeoAlerta Sul is not an official warning or evacuation authority. Sensitive personal records, private infrastructure, restricted datasets and proprietary implementation details will not be published. Only legally redistributable datasets or approved derived summaries will be shared. Geographic portability is a design ambition, not evidence of universal predictive performance.

## 9. Provisional timeline

Months 1–6: systematic literature review, source audit and dataset freeze. Months 7–12: event-level model reproduction and baselines. Months 13–20: uncertainty and sensor failure studies. Months 21–28: spatial validation and transfer to a second basin. Months 29–36: expert study, synthesis, papers and thesis. The timeline is illustrative, not a confirmed UPC program duration.

## References

[1] Mantovani et al., *npj Natural Hazards* (2024), DOI https://doi.org/10.1038/s44304-024-00009-8 .  
[2] Wilkinson et al., *Scientific Data* (2016), DOI https://doi.org/10.1038/sdata.2016.18 .  
[3] WMO, *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364 (2025), https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts .

**Internal primary evidence:** [P1] `docs/EVENT_VALIDATION.md` (Git blob `aea7158b7e7324cf65175f64c472978eb5b23b08`); [P2] `models/evaluation/topography-emsr720-loao.json` (Git blob `a5554c09afbc88464cea5896fdd755060484a73c`), private GeoAlerta Sul, accessed 2026-10-26.
