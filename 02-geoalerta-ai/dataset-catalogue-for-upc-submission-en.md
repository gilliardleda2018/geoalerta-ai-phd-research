# GeoAlerta Sul — Dataset Catalogue, Lineage and Reproducibility Appendix

**Release:** 26 October 2026, candidate-review draft. **Evidence:** static inspection of five private `geoalerta-sul` JSON evaluation/manifests; no raw source files or independent SHA-256 validation. This appendix accompanies the four documents requested by Prof. Carlos Cano Domingo.

## D1 — ANA historical hydrometry (2021–2025)

Primary artifact: `models/evaluation/critical-events-v5-ana-history-manifest.json` (Git blob `f575fae1780b751b7421a2223cba04c92cb1ca71`). Original provider: legacy ANA `DadosHidrometeorologicos` web service, described in the manifest as deprecated and backed by a secondary database. The manifest records **55 annual station-file entries**, **11 station identifiers**, **1,376,063 total normalized rows** and **8 zero-row entries**. Counts are sums of manifest `row_count` fields, not unique timestamp counts or independently downloaded observations.

| Year | Station-files | Manifest rows | Zero-row station-files |
|---|---:|---:|---:|
| 2021 | 11 | 182,216 | 3 |
| 2022 | 11 | 250,115 | 3 |
| 2023 | 11 | 247,414 | 2 |
| 2024 | 11 | 313,851 | 0 |
| 2025 | 11 | 382,467 | 0 |

Station identifiers: `bom_retiro_do_sul`, `encantado`, `estrela`, `linha_colombo`, `linha_jose_julio`, `mucum`, `passo_carreiro`, `passo_tainhas`, `porto_mariante`, `santa_tereza`, `taquari`. Each entry includes source ANA code, observation timestamps, raw XML source hash, normalized CSV hash and status. Zero-row stations are missing coverage, not zero river level.

## D2 — ANA flood-event windows

Primary artifact: `models/evaluation/critical-events-v5-ana-manifest.json` (Git blob `f5bff13c28526f8fd44829f28ebef130e8787fae`). **44 event-station entries**, **25,641 manifest rows**, **7 zero-row entries**.

| Event identifier | Station-files | Rows | Zero-row files |
|---|---:|---:|---:|
| taquari-2023-09 | 11 | 3,366 | 3 |
| taquari-2023-11 | 11 | 4,940 | 2 |
| taquari-2024-05 | 11 | 7,995 | 2 |
| taquari-2025-06 | 11 | 9,340 | 0 |

The original XML and normalized CSV are recorded separately with SHA-256. These totals are **not** the number of independent flood episodes or the number of valid forecast origins. The event benchmark evaluates whole-event holdouts; candidate model selection on outer-event labels is an identified methodological flaw, to be repaired through nested selection.

## D3 — SGB/SACE Taquari monitoring snapshot

Primary artifact: `models/evaluation/critical-events-v5-sgb-manifest.json` (Git blob `679ee7eb17482b5001398a61fdf304d3fada1528`). The manifest lists **27 CSV source files**, **16 station identifiers**, river stage (`cota`) and rainfall (`chuva`) variables, **1,783,721 recorded source bytes**. Each entry records station ID, SACE `pm_id`, variable, URL, byte size and SHA-256. The byte total is a sum of manifest values, not a present-day download-size guarantee. Example recorded source: `https://www.sgb.gov.br/sace/sace_nivel/api/dados/taquari_1_cota.csv` (Estrela, stage). Direct URL accessibility and terms of reuse must be checked separately.

## D4 — Copernicus EMSR720 observed-event spatial reference

Primary artifact: `models/evaluation/topography-emsr720-loao.json` (Git blob `a5554c09afbc88464cea5896fdd755060484a73c`). Three observed-event flood delineation files and recorded SHA-256:

| AOI | Positive samples | Comparison negatives | Recorded SHA-256 |
|---|---:|---:|---|
| Encantado | 184 | 552 | `4721fd30738090cb52ad311c147dc3fee045c3bc42496a128592dfc153dc4717` |
| Roca Sales | 254 | 762 | `dde36b381f72ed09b44efa4fd53fbe144839a15504bf459095d02a1996b67d3c` |
| Santa Tereza | 66 | 198 | `b6c9d2050a0e8941409f413acaba02fcb3c79250ec4e89d30e242a716dfb179f` |

**Total:** 2,016 comparison points (504 positive, 1,512 comparison negative). The constructed 25% positive prevalence is a sampling choice, not geographic prevalence. Evaluation uses leave-one-AOI-out ExtraTrees; mean ROC-AUC baseline 0.8444 versus enriched 0.8464; enriched model **not promoted**. All three AOIs are within the same 2024 event. Public catalogue: https://rapidmapping.emergency.copernicus.eu/EMSR720/ .

## D5 — Lajeado simulated municipal stage references

Primary artifact: `models/evaluation/lajeado-municipal-stages.json` (Git blob `9020b12634cd9068fbfee4455e8ad02266baf259`). 18 simulated stage polygons, 11,235 comparison points, 90m reported grid spacing. The source manifest records:
- `data/raw/municipal/lajeado/cotas_ate_30.kmz`: `b00a63a889bad793f936d3b785ce6a1620d04332f2bb5993857ee8d89c7dc909`.
- `data/raw/municipal/lajeado/enchente_maio_2024.kmz`: `33eb00915e7f59f9b47b844c2e9868d51be85041092d84580e16bec567e95de6`.
- `data/raw/municipal/lajeado/ibge_boundary.geojson`: `4f3094d6564b55ccd6f76a0d32248c45b4ccb3e8daed92fbc677004de52995e5`.

Mean static ROC-AUC 0.8007 is **conformance against simulated maps**, not observed-event validation. Stage 21m reports precision 1.92% and recall 100% for the rule. Geometry irregularities at stages 31m and 33m and selection of optimal thresholds on evaluation labels require independent review.

## D6 — Other context and exposure families

The source catalogue also identifies meteorological/satellite context (GOES-19, ECMWF, Earth-2/AIFS ENS, Sentinel-1 SAR), DEM/HAND/drainage layers, IBGE aggregated population, CNES facilities and SEDEC/S2ID disaster history. **These are documented source families, not a complete byte-verified inventory of every ingested record**. Earth-2 is context-only; source presence does not imply trained forecast skill or current availability. Exact provider product versions, access rights and acquisition times remain pending.

## Dataset-to-model-to-evaluation traceability

| Dataset | Processing | Model or heuristic | Validation |
|---|---|---|---|
| ANA 2021–2025 history | XML to normalized CSV; timestamp/station alignment | Critical-stage logistic classifier and false-alarm screening | Negative-period episodes; Brier, false positives |
| ANA four event windows | Station joins, stage lags, historical features | River-stage regression candidates | Four-event LOEO; nested repair required |
| SGB/SACE CSV | Stage/rain source normalization and lag/rolling features | Operational baseline / hybrid forecast | July temporal holdout and historical comparisons, separate cohorts |
| Copernicus EMSR720 + terrain | AOI grid, flood labels, HAND/slope/drainage | ExtraTrees spatial classification | Three-AOI leave-one-area-out |
| Lajeado KMZ + IBGE boundary | Simulated-stage polygons and grid comparison | Static risk heuristic | Municipal conformance only |
| IBGE/CNES and other context | Aggregate spatial overlay | Exposure-priority rule | No independently validated casualty/damage outcome |

## Reproducibility and disclosure gates

**Verified from manifests:** identifiers, recorded timestamps, row counts, file sizes, source hashes, stored evaluation summaries. **Not verified:** original bytes, SHA-256 recomputation, source licenses, exact full raw dataset availability, independent model reruns, cross-basin skill, API production status. Do not treat manifest row counts as unique records across datasets; annual and event windows overlap.

The academic package should share this catalogue, public institutional links, evaluation protocols and aggregate statistics. **Do not attach private raw XML/CSV, licensed municipal geometry, model joblib, cloud secrets or personal data** without explicit permission and licensing review. If the professor requests reproducible data, offer a controlled research-access discussion, approved derived samples or provider-specific access instructions.

### Reference hierarchy
Internal source manifests and model reports are primary technical evidence, not peer-reviewed studies. Public institutional entry points: SGB/SACE https://www.sgb.gov.br/sace/ ; ANA https://www.ana.gov.br/hidrowebservice/swagger-ui/index.html ; Copernicus https://rapidmapping.emergency.copernicus.eu/ ; IBGE https://www.ibge.gov.br/ ; CNES https://cnes.datasus.gov.br/ . WMO (2025) forecast verification and Wilkinson et al. (2016) FAIR principles provide methodological context.
