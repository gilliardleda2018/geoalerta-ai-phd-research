# GeoAlerta Sul — Doctoral Research Statement (Draft)

**Audience:** Prof. Carlos Cano Domingo, UPC BarcelonaTech. **Status:** candidate draft; not submitted.

## Research motivation
The severe floods in the Vale do Taquari exposed the need to interpret hydrological, meteorological, remote-sensing and territorial evidence across multiple institutions and temporal scales. GeoAlerta Sul is an existing experimental geospatial decision-support implementation that provides a testbed for investigating the reliability of such integration. It does not replace SGB, ANA or Defesa Civil official warning authority.

## Research question
Can provenance-aware data fusion, calibrated uncertainty and safety-constrained model governance improve flood-risk decision support under missing observations, distribution shift and geographic transfer?

## Existing technical evidence
The private GeoAlerta Sul repository documents Python services, source and quality contracts, hydrological forecasting, ExtraTrees residual correction, critical-event classifiers, terrain/HAND indicators, exposure prioritization and evaluation scripts. A four-event within-basin leave-one-event-out report gives mean RMSE 0.0804 m at 1h and 0.6878 m at 6h, versus persistence 0.2833 m and 1.6798 m respectively. A three-area EMSR720 spatial holdout reports baseline ExtraTrees ROC-AUC 0.8444 and enriched 0.8464, but enrichment fails its promotion gate. The critical v6 challenger was rejected for excessive false positives. These are repository-reported retrospective results, not independently reproduced scientific findings.

## Proposed doctoral contributions
1. A provenance and data-quality benchmark for heterogeneous flood observations.
2. Event-wise verification of hybrid predictors, calibrated exceedance probabilities and false-alarm burdens.
3. Auditable human-in-the-loop model promotion and uncertainty communication.
4. A measurable cross-city portability protocol evaluating adaptation cost and predictive degradation.

## Research limitations and integrity
Only four flood episodes in one basin have been reported for event-level evaluation; the Copernicus geographic holdout covers one event. Municipal simulated-stage conformance is not independent observed flood validation. Predictive portability to any city is an architectural aspiration requiring new-basin tests. No automatic public-warning authority is claimed. Dataset checksums, training reruns, deployment state and individual authorship require further verification.

## References
[1] Mantovani et al., npj Natural Hazards (2024), https://doi.org/10.1038/s44304-024-00009-8 .
[2] Wilkinson et al., Scientific Data (2016), https://doi.org/10.1038/sdata.2016.18 .
[3] WMO, Guidelines on the Verification of Hydrological Forecasts (2025), https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts .

**Primary internal evidence:** private `gilliardleda2018/geoalerta-sul`, `docs/EVENT_VALIDATION.md`, `docs/ML_MODEL_CARD.md`, `models/evaluation/topography-emsr720-loao.json`, `models/evaluation/critical-events-stage5-decision.json`. Freeze exact source revision before submission.
