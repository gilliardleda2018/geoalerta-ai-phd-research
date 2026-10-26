# Municipal-stage validation — threshold-selection and construct-validity audit

**Date:** 2026-10-26. **Source:** private `gilliardleda2018/geoalerta-sul` branch main, `scripts/evaluate_municipal_stages.py`, `app/terrain.py`, `models/evaluation/lajeado-municipal-stages.json`, `docs/MUNICIPAL_STAGE_CONFORMANCE.md`. **Method:** read-only source review, no execution. This is a methodological finding, not a production incident.

## Finding MUN-001: threshold selection on evaluation labels

The function `optimal_static_threshold(labels, scores)` enumerates thresholds from the evaluated scores and selects the best according to balanced accuracy, with F1 as tie-breaker. In `main()`, this function is applied to the same municipal grid labels used to report `optimal_static_cutoff` precision, recall, F1 and balanced accuracy. Consequently, the optimized-cutoff performance is **in-sample threshold selection**, not an unbiased independent estimate of threshold generalization. The separately reported continuous-score ROC-AUC is not directly optimized by this cutoff routine, but remains a conformance metric against simulated maps.

**Recommendation:** fit threshold only on training municipalities/stages/events; freeze it; evaluate on independent held-out municipal geometry or observed flood extents. Report train/validation/test separation and prevalence. Do not advertise the optimized-cutoff precision as externally validated.

## Finding MUN-002: simulation-to-observation construct mismatch

The reference polygons are municipal *simulated flood-stage* maps, not independent observed flood footprints. The report notes potential shared terrain assumptions, photogrammetric elevation from 2011/2012, and that the Estrela gauge reading is not identical to absolute ground elevation throughout Lajeado. Agreement with these polygons is **map conformance**, not empirical validation of actual inundation, flood depth or evacuation safety.

## Finding MUN-003: geometry and saturation controls

The source flags a stage if mapped area shrinks versus the preceding stage or grows by more than 75%; the stored report identifies stages 31m and 33m for review. The uniform dynamic risk rule flags 10,144/11,235 cells at stages >=21m (90.29% of the grid), yielding high recall but poor precision for sparse flood footprints. These are repository-reported descriptive findings; no new rerun was performed.

## Validation plan

1. Obtain and license-check official Lajeado stage polygons and IBGE boundary, retaining original KML/KMZ and SHA-256.
2. Verify CRS, vertical references, stage ordering, geometry validity, topology, and 31m/33m anomalies.
3. Reproduce grid sampling and scores from frozen terrain rasters; compare source manifest hashes.
4. Separate threshold selection from final evaluation (prefer independent event/area holdout). Report precision, recall, F1, specificity, balanced accuracy and confusion matrices per stage with denominator and positive prevalence.
5. Compare to independent surveyed high-water marks or observed satellite flood footprints when available; quantify geographic and event uncertainty.
6. Do not promote stage-conditioned risk classes or claim safe evacuation routes without institutional review.

## Academic conclusion

The reported mean static ROC-AUC of 0.8007 is evidence of **retrospective conformance to municipal simulated flood-stage maps**. The optimized-cutoff performance is not independently validated. This distinction should be explicit in the doctoral dossier, rather than presenting both as equivalent forms of predictive skill.

## References

[P1] Private source paths listed above, inspected 2026-10-26; repository-reported results not independently reproduced.

[1] WMO, *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364, 2025, https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts .

[2] Wilkinson et al., *The FAIR Guiding Principles for scientific data management and stewardship*, *Scientific Data* 3, 160018 (2016), https://doi.org/10.1038/sdata.2016.18 .
