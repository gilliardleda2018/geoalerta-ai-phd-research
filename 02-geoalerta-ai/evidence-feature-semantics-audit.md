# GeoAlerta Sul — Evidence normalization and feature semantics audit

**Date:** 2026-10-26. **Method:** read-only source review; tests not executed. Source: private `gilliardleda2018/geoalerta-sul`, `app/data_catalog.py`, `app/evidence.py`, `app/features.py`, `app/ml_inference.py`, `tests/test_evidence.py`, `tests/test_features.py`. This document is a technical observation, not a production incident report.

## Positive implementation evidence

- `RawEvidenceRecord` uses Pydantic with forbidden extra fields, registered source IDs, timezone-aware observation/ingestion timestamps, finite numeric values, variable-specific units, geographic coordinate bounds and rainfall accumulation metadata.
- Normalization converts river levels from centimetres to metres, percent to fractions, preserves source/quality metadata, and creates evidence identifiers.
- Feature assembly excludes other geographic areas and future-dated observations; outputs evidence IDs, minimum quality, missing feature names and exclusion counts.
- Tests define examples for unit conversion, deduplication, quality flags, unknown sources, missing accumulation period, traceable feature assembly and future/other-area exclusions.

## Finding F-01: rainfall accumulation-window overlap requires audit

`app/features.py` groups rainfall observations by source and sensor and **sums each record's value** inside a rolling 1h/6h/24h observation-time window, without using `aggregation_minutes` to verify that accumulation windows do not overlap or extend before the window boundary. For example, a 24h accumulation published within a 6h window could be included in the 6h sum. Whether this occurs in production depends on upstream normalized records; it is not established by code alone.

**Proposed test:** mix 15-minute, hourly and 24-hour accumulation records from the same sensor, with overlap and missing intervals; compare feature result with a time-interval-aware ground truth. Define inclusive/exclusive window policy and duplicate/overlap treatment.

## Finding F-02: gauge-level fusion must respect vertical datum

`_latest_level` selects the most recent river-level timestamp across records in the area and averages equal-quality records at that timestamp, without explicit station identity or vertical-datum harmonization in this function. The broader source documentation warns against cross-gauge datum mixing. The feature function's contract should therefore specify whether it is only called with a single calibrated target gauge or add explicit target-station and datum checks.

**Proposed test:** two gauges with distinct native reference levels and equal quality/timestamps must never be averaged as though they shared a datum. Verify actual callers before assigning operational severity.

## Finding F-03: data freshness and accumulation semantics

`assemble_features` filters future records but does not impose a per-source maximum age on river-level selection in the inspected function. The main forecasting path separately rejects stale observations, so this is a **function-level contract gap**, not proof of a global freshness failure. Define whether the assembled feature snapshot is descriptive or forecast-admissible and enforce the corresponding age policy at the correct boundary.

## Finding F-04: empirical interval terminology

`app/ml_inference.py` creates lower/upper bounds using a stored `residual_abs_90_m` constant around each prediction. This is a symmetric empirical residual envelope, not necessarily a calibrated 90% prediction interval under event shift. Verify held-out empirical coverage and interval width by event and horizon before describing it as a statistically valid 90% interval.

## Priority validation matrix

| ID | Case | Required evidence |
|---|---|---|
| V01 | Overlapping rainfall accumulation periods | interval-aware feature tests, per-source schema and corrected expected totals |
| V02 | Mixed river gauges/vertical references | station and datum gate, negative test with equal timestamps |
| V03 | Stale source in feature assembly | documented contract and boundary test |
| V04 | Residual interval coverage across independent events | coverage and width by event/horizon; calibrated comparison |
| V05 | Duplicate source observations | normalized identity and collision handling tests |
| V06 | Source trust versus quality weighting | ablation and documented rationale, including biased source scenarios |

## Scientific relevance

These are concrete examples of why *provenance-aware data fusion* is a substantive methodological question rather than merely collecting APIs in one dashboard. The project should quantify how temporal support, spatial identity, sensor datum and uncertainty affect downstream flood predictions. No novelty or improved accuracy is claimed without experiments.

## Citation framing

Wilkinson et al. (2016), *Scientific Data* 3:160018, https://doi.org/10.1038/sdata.2016.18 (FAIR and provenance); WMO (2025), *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364, https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts (forecast verification).
