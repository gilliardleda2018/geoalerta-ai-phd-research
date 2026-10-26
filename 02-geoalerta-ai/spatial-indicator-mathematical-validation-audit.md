# GeoAlerta Sul — Spatial indicators: mathematical definitions and validation threats

**Date:** 2026-10-26. **Scope:** read-only inspection of private `gilliardleda2018/geoalerta-sul` code and reported EMSR720 metrics. **Evidence level:** source-code observation plus repository-reported results, **not independently rerun**.

## 1. Exact implementation and mathematical interpretation

`app/terrain.py:score_terrain_risk` defines a deterministic, piecewise susceptibility score (not a trained flood probability):

\[
S_{static}=0.55 S_{HAND}+0.30 S_{drainage-distance}+0.15 S_{slope}.
\]

A separate hydrological component is assigned from the margin between the **upper forecast bound** and a local critical gauge threshold. The combined score is:

\[
S_{combined}=\min(1,0.55 S_{dynamic}+0.45 S_{static}),
\quad P_{priority}=\min(1,S_{combined}\min(1.5,\sqrt{w_{exposure}})).
\]

The model assigns categorical levels at combined-score cutoffs 0.35, 0.60 and 0.80. These cutoffs and weights are **engineering heuristics**, not independently calibrated probabilities of inundation, casualty or evacuation need. They must not be interpreted as such in academic charts.

## 2. Topographic data transformation

`TerrainRiskEngine` reads Copernicus DEM GLO-30 GeoTIFF, HAND GeoTIFF and SGB Taquari drainage GeoJSON. It computes slope from finite differences, 3-cell and 11-cell TPI, local roughness, Laplacian curvature proxy, and nearest drainage distance after projecting to EPSG:31982. It records source-file SHA-256 hashes. These are observed implementations; correctness depends on raster georeferencing, CRS and nodata handling.

## 3. Spatial validation method

`scripts/calibrate_topography_emsr720.py` samples AOI bounding boxes at approximately 90m spacing using latitude/longitude increments based on the midpoint latitude, filters points to the AOI polygon, labels flood positives by `flooded.intersects(point)`, and selects up to 3 non-flood-delineated comparison negatives per positive (seed 720). It fits `ExtraTreesClassifier` with 400 estimators, min_samples_leaf=5, max_features='sqrt', balanced class weights and random_state=720, holding out one entire AOI at a time. It computes ROC-AUC, average precision and balanced accuracy at probability cutoff 0.5.

**Important:** the 90m grid is approximate rather than a uniform projected metric grid. Confirm AOI geometry CRS, pixel sampling coordinates, polygon validity and area weighting before interpreting spatial accuracy. This is a proposed check, not a confirmed defect.

## 4. Observed results and non-promotion

`models/evaluation/topography-emsr720-loao.json` reports 2,016 points (504 positives; 1,512 comparison negatives) from Encantado, Roca Sales and Santa Tereza. Mean ROC-AUC baseline 0.8444 vs enriched 0.8464 (+0.0020). Roca Sales deteriorated from 0.7657 to 0.7489. The model correctly records `operational_promotion_allowed=false` because the gate requires improvement >=0.02 and no held-out AOI decline.

These upstream AOIs do not directly validate Lajeado/Estrela. The same 2024 flood event appears across areas, so the experiment tests **within-event geographic holdout**, not cross-event or cross-basin generalization.

## 5. Additional validation threats to investigate

- **Prevalence distortion:** 3:1 comparison-negative sampling produces 25% positive prevalence; precision/AP and fixed-threshold operating points may not transport to the full map. Compare full-AOI negative population and report calibration under actual geographic prevalence.
- **Label uncertainty:** Copernicus non-delineated pixels can reflect sensor shadow, vegetation, incomplete acquisition or mapping omission. Treat these as comparison negatives, not proven dry land.
- **Spatial dependence:** points on a regular grid within each AOI are autocorrelated; confidence intervals should use AOI/event-block units, not naive independent point bootstrap.
- **CRS and nodata:** verify DEM/HAND CRS, horizontal/vertical datum, reprojection consistency, nodata propagation, raster edge behavior and SGB drainage topology.
- **Threshold choice:** 0.5 balanced accuracy and categorical heuristic thresholds require explicit decision-utility justification and independent calibration.
- **Data leakage:** examine whether any topographic context proxy or engineered feature encodes label geography indirectly, and whether all tuning was confined to training AOIs.

## 6. Reproduction protocol (proposed, not run)

1. Freeze the source commit and record raster and Copernicus source hashes, license and coverage.
2. Rebuild AOI sampling in an isolated environment, verify exact counts and geometry; compare approximate lat/lon grid with equal-area projected grid.
3. Run original ExtraTrees baseline/enriched with pinned scikit-learn, record per-AOI AUC, AP, balanced accuracy and confusion matrices.
4. Repeat with all valid comparison negatives; evaluate precision, prevalence, calibration and confidence intervals.
5. Repeat on independent observed flood events and in Lajeado/Estrela when permitted ground truth exists.
6. Do not promote model or update production from this research workflow.

## References

[P1] Private source `app/terrain.py`, `scripts/calibrate_topography_emsr720.py`, `models/evaluation/topography-emsr720-loao.json` (inspected 2026-10-26).

[1] World Meteorological Organization, *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364, 2025. https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts .

[2] M. D. Wilkinson et al., “The FAIR Guiding Principles for scientific data management and stewardship,” *Scientific Data*, 3:160018, 2016. https://doi.org/10.1038/sdata.2016.18 .

**Academic conclusion:** the project has an auditable deterministic susceptibility model and a separate supervised topographic comparison. Neither score is automatically a validated flood probability. The geographic evaluation is valuable but limited; independent event and local pilot validation are required.
