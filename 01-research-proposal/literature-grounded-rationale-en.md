# Literature-grounded doctoral rationale (draft)

The Vale do Taquari provides a documented real-world setting for investigating geospatial flood intelligence. Mantovani et al. [1] analyze the September 2023 extreme rainfall surge in the Taquari-Antas basin; Soares and Marcuzzo [2] document preliminary inundation mapping for 2023–2024 in Encantado and Muçum; Borges et al. [3] study hydrological disaster processes in Estrela and Lajeado. These studies establish the relevance and complexity of the regional hazard; they do not evaluate GeoAlerta Sul.

The scientific problem extends beyond forecasting accuracy. The FAIR principles [5] provide a conceptual foundation for machine-actionable provenance and reuse. Recent flood-prediction and uncertainty reviews [6,7] identify continuing challenges in heterogeneous data integration, predictive calibration, uncertainty propagation and operational decision support. The doctoral project should test whether GeoAlerta Sul's source-quality gates, model comparisons and decision-facing evidence presentation produce measurable improvements under missing, delayed or geographically shifted data.

**Claim boundary:** the GeoAlerta Sul repository demonstrates source adapters, provenance-oriented contracts, retrospective modeling and an Lajeado/Estrela pilot, but independent reproduction, prospectively calibrated uncertainty and out-of-basin generalization are not yet established. Portability to any city is an architectural design ambition requiring local data, hydrological configuration, governance and validation.

## Testable contributions
- A provenance-aware integration benchmark with explicit source latency, datum, missingness and quality controls.
- Multi-event and prospective validation of hybrid level and threshold-exceedance forecasting.
- Quantified reliability and uncertainty coverage under sensor failure and domain shift.
- A reproducible transfer protocol across municipalities/basins, evaluated against fixed baselines and operational decision costs.

## References
See `05-literature-review/verified-bibliography-2026-10.md` for [1]–[8].
