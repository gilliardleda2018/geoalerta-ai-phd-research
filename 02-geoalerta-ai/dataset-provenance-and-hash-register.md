# GeoAlerta Sul — Dataset provenance and reproducibility register (v1)

**Audit date:** 2026-10-26  
**Case:** Vale do Taquari, Brazil  
**Implementation:** private `gilliardleda2018/geoalerta-sul` (read-only)  
**Evidence classification:** `REPOSITORY_REPORTED_RESULT` and `REPOSITORY_OBSERVED`; **NOT** `INDEPENDENTLY_REPRODUCED`.

## 1. Scientific purpose

This register links the municipal-stage and Copernicus EMSR720 evaluation reports to their named source files and reported SHA-256 values. A SHA-256 in a manifest is a **claim about source integrity**, not proof of integrity until the referenced file's actual bytes have been read and hashed. Source files may be private or subject to provider licensing. No raw source bytes or private operational data are reproduced here.

## 2. Source evaluation reports and Git object identifiers

| Evidence ID | Report | Git blob SHA (identifies Git content, not the source dataset's SHA-256) | Reported result |
|---|---|---|---|
| EV-GEO-001 | `models/evaluation/topography-emsr720-loao.json` | `a5554c09afbc88464cea5896fdd755060484a73c` | 2,016 sampled points; mean baseline ROC-AUC 0.8444, enriched 0.8464; no promotion |
| EV-GEO-002 | `models/evaluation/lajeado-municipal-stages.json` | `9020b12634cd9068fbfee4455e8ad02266baf259` | 18 simulated stages; 11,235 points; mean static ROC-AUC 0.8007; no promotion of uniform stage rule |

The Git blob SHA and SHA-256 values serve different purposes and must never be conflated. Pin the complete source commit SHA before formal academic submission.

## 3. Copernicus EMSR720 dataset manifest

The following values are transcribed from `topography-emsr720-loao.json`; **source-byte verification remains pending**.

| AOI | Relative file | Recorded SHA-256 | Positives | Comparison negatives |
|---|---|---|---:|---:|
| Encantado | `data/raw/copernicus_ems/EMSR720/AOI02_Encantado/EMSR720_AOI02_DEL_MONIT01_observedEventA_v1.json` | `4721fd30738090cb52ad311c147dc3fee045c3bc42496a128592dfc153dc4717` | 184 | 552 |
| Roca Sales | `data/raw/copernicus_ems/EMSR720/AOI03_Roca_Sales/EMSR720_AOI03_DEL_PRODUCT_observedEventA_v1.json` | `dde36b381f72ed09b44efa4fd53fbe144839a15504bf459095d02a1996b67d3c` | 254 | 762 |
| Santa Tereza | `data/raw/copernicus_ems/EMSR720/AOI05_Santa_Tereza/EMSR720_AOI05_DEL_MONIT01_observedEventA_v1.json` | `b6c9d2050a0e8941409f413acaba02fcb3c79250ec4e89d30e242a716dfb179f` | 66 | 198 |

**Totals:** 504 positives, 1,512 sampled comparison negatives, 2,016 points. Each AOI has 25% positive sample prevalence by construction; this is not necessarily the real flood-area prevalence.

Public provider catalogue: https://rapidmapping.emergency.copernicus.eu/ . Obtain a verified product-specific link, release identifier, acquisition time and reuse terms before offering the dataset for download.

## 4. Lajeado municipal reference manifest

Values are transcribed from `lajeado-municipal-stages.json`; **source-byte verification remains pending**.

| Relative file | Recorded SHA-256 |
|---|---|
| `data/raw/municipal/lajeado/cotas_ate_30.kmz` | `b00a63a889bad793f936d3b785ce6a1620d04332f2bb5993857ee8d89c7dc909` |
| `data/raw/municipal/lajeado/enchente_maio_2024.kmz` | `33eb00915e7f59f9b47b844c2e9868d51be85041092d84580e16bec567e95de6` |
| `data/raw/municipal/lajeado/ibge_boundary.geojson` | `4f3094d6564b55ccd6f76a0d32248c45b4ccb3e8daed92fbc677004de52995e5` |

The municipality's polygons are described by the repository as **simulated flood-stage maps**; they are not independent observed flood events. The stored report flags stages 31 m and 33 m for geometry review. These maps and the Estrela gauge do not necessarily share an identical absolute vertical reference at every location.

## 5. Integrity verification protocol — not yet executed

1. Obtain lawful access to each exact dataset version; preserve the original file unchanged.
2. Record original provider, acquisition date, product version, geographic footprint, license and source URL.
3. Compute SHA-256 on original bytes and compare against the recorded manifest.
4. Preserve verification logs and explicitly mark mismatch, unavailable file or license restriction.
5. Verify GeoJSON/KMZ geometry, coordinate reference system, vertical datum, nodata handling and spatial coverage.
6. Freeze Python environment and source commit; run evaluation scripts on an isolated machine with no production credentials.
7. Record per-area/per-stage confusion matrices, prevalence, ROC-AUC, average precision, calibration, error analysis and any deviation from repository-reported metrics.
8. Publish only permitted source links, aggregate results, code revision, methodological description and approved derived outputs.

## 6. Public disclosure classification

- **Suitable for public academic dossier:** official portal links, scientific citations, non-sensitive aggregate metrics, general algorithms, evaluation protocols and recorded hashes.
- **Requires review:** exact raw datasets, provider redistribution terms, licensed municipal geodata, private repository implementation details and generated model artifacts.
- **Never publish here:** cloud credentials, restricted telemetry, personal data, secret endpoints, internal storage paths identifying protected resources or operational emergency access.

## 7. Claim allowed in the UPC submission

> The GeoAlerta Sul repository contains versioned retrospective evaluation reports and source manifests with recorded SHA-256 values for Copernicus EMSR720 flood delineations and municipal stage-map inputs. Independent verification of the original file bytes and reproduction of the reported model metrics remain outstanding.

## 8. References

[1] M. D. Wilkinson et al., “The FAIR Guiding Principles for scientific data management and stewardship,” *Scientific Data*, vol. 3, 160018, 2016. https://doi.org/10.1038/sdata.2016.18 .

[2] World Meteorological Organization, *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364, 2025. https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts .

[3] Copernicus Emergency Management Service, Rapid Mapping portal. https://rapidmapping.emergency.copernicus.eu/ .

**Next milestone:** hash actual authorized source bytes and produce an immutable reproducibility log; do not claim verification until performed.
