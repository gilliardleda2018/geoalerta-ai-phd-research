# GeoAlerta Sul — Data lineage, calibration gates and false-alarm methodology audit

**Date:** 2026-10-26. **Evidence type:** static source inspection, not experiment replication. **Exclusive implementation:** private `gilliardleda2018/geoalerta-sul`. This public dossier contains no raw private data.

## Inspected artifacts and code

- `models/evaluation/critical-events-v5-ana-history-manifest.json` — annual 2021–2025 ANA source records, station IDs, original XML hashes, normalized CSV hashes, row counts, timestamps and source warning.
- `models/evaluation/critical-events-v5-ana-manifest.json` — event-specific ANA windows and per-file hashes.
- `models/evaluation/critical-events-v5-sgb-manifest.json` — SGB/SACE URLs, station/variable identity, byte size and SHA-256 per source CSV.
- `scripts/evaluate_non_event_false_alarms.py` — negative-period evaluation and episode grouping.
- `app/target_calibration.py` — target-specific shadow eligibility gates.
- `models/evaluation/critical-events-non-events-2021-2025-v1.json`, `critical-events-v6-shadow.json`, `critical-events-stage5-decision.json` — reported metrics and promotion decision.

## Data provenance: observed design

The annual ANA manifest states the upstream endpoint is the deprecated `telemetria1ws/ServiceANA.asmx/DadosHidrometeorologicos` service, backed by a secondary database. It preserves compressed raw XML and normalized CSV with distinct hashes, plus row counts and observation windows. Missing values remain null. The SGB manifest records each CSV URL and checksum. This is substantial **manifest-level traceability**, but not proof of end-to-end checksum verification without access to the referenced bytes and rerunning verification.

**Missingness example:** the event ANA manifest includes station `linha_colombo` with zero records for September 2023; the annual ANA history manifest also records zero rows for this station in 2021. The presence of a station in a catalog is not evidence of observed data coverage. Coverage must be calculated per station, variable, event and origin time.

## False-alarm evaluation: code-level findings

The script builds a continuous historical dataset from normalized annual CSV files, merges by station, sorts timestamps and drops duplicate timestamps keeping the last occurrence. Known event windows are excluded with a configurable three-day buffer. A negative origin is a below-threshold target outside buffered known events. It loads horizon-specific `CriticalEventArtifact` joblib files and computes probabilities using the artifact model and optional calibrator.

**Episode definition:** predicted positive origins separated by <=30 minutes are grouped; episodes with >=4 positive origins are called persistent. The code computes episode signal-hours as number of positive origins multiplied by 0.25 hours, assuming nominal 15-minute cadence. This metric measures **positive-origin equivalents**, not necessarily elapsed wall-clock warning duration when timestamps are missing or irregular. The published scientific report should either retain that precise label or additionally compute actual time spans with explicit gap policy.

**Selection and independence caution:** event exclusions rely on the configured known-event list and buffers. Unrecorded floods or upstream sensor anomalies may contaminate negative windows. Verify full coverage, missingness, sampling interval and model-selection independence before treating false-positive rates as prospective operational reliability.

## Target calibration gates: code-level findings

`app/target_calibration.py` rejects duplicate target IDs and requires 24 h in the globally blocked horizons. For a target to become `eligible_for_shadow`, the target must have a verified official/municipal stage, an evaluation with recall, precision, false-negative rate, false-positive rate and Brier, at least two independent episodes, and `leakage_check == passed`. The function returns **shadow comparison only**, never public warning or automatic model promotion. This is an eligibility contract, not evidence that all real municipal targets satisfy it.

## Evidence classification

- **Verified from inspected code:** explicit gating conditions, manifest schema fields, episode grouping implementation and file-path references.
- **Reported by stored artifacts:** counts, rates, model comparisons and rejection decision.
- **Not verified:** upstream raw bytes, hash integrity, actual model joblib behavior, live collector freshness, deployed gate configuration, production alerts, independent prospective skill.

## Priority experiments

1. Download permitted raw source samples to isolated research storage and verify checksums against both ANA and SGB manifests.
2. Produce station×event×variable coverage table, missingness distribution, timestamp continuity and datum registry; test zero-row stations.
3. Recompute non-event false-positive rates and compare positive-origin hours against elapsed wall-clock durations under a declared gap rule.
4. Reproduce calibration and model-selection thresholds with untouched chronological holdout; document every threshold decision.
5. Test target registry invalid inputs and ensure 24 h never becomes eligible; test missing official datum and fewer than two independent episodes.
6. Record all commands, dependency hashes, logs, metrics and reviewer before claiming replication.

## Academic significance

This is a concrete research opportunity in provenance-aware sensor fusion, robust uncertainty, event-level reliability and safety-constrained promotion. It must be compared with peer-reviewed methods and independent baselines before any claim of novelty. For verification principles consult WMO (2025), *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364, https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts and Wilkinson et al. (2016), DOI https://doi.org/10.1038/sdata.2016.18.
