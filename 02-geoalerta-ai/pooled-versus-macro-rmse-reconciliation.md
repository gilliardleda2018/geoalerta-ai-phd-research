# GeoAlerta Sul — Second-stage numerical reconciliation: pooled versus event-mean RMSE

**Date:** 2026-10-26. **Status:** independent deterministic arithmetic from repository JSON, NOT model reproduction. **Source:** private `gilliardleda2018/geoalerta-sul`, `models/evaluation/ana-events-loeo-v1.json` (Git blob `959b909840dd47ba75c64790a36dedb2337b8393`) and `docs/EVENT_VALIDATION.md` (Git blob `aea7158b7e7324cf65175f64c472978eb5b23b08`).

## Hypothesis tested

Could the differences between narrative mean RMSE and the JSON mean be explained by weighting each flood event by its number of valid forecast origins?

Two valid but distinct summaries were recalculated:
- **Macro mean** = average of four per-event RMSE values, giving each flood event equal weight.
- **Micro/pooled RMSE approximation** = sqrt(sum(n_event × RMSE_event²) / sum(n_event)), weighting squared errors by valid origins. The JSON RMSEs are rounded to 4 decimals, so pooled values are approximations, not exact raw-residual recomputations.

## Numerical results

| Horizon | Narrative RMSE | JSON macro mean of selected candidates | JSON pooled RMSE of selected candidates | Valid origins |
|---|---:|---:|---:|---:|
| 1h | 0.080400 | 0.078525 | 0.073995 | 1,766 |
| 2h | 0.176600 | 0.174300 | 0.161010 | 1,744 |
| 3h | 0.290400 | 0.286975 | 0.267283 | 1,723 |
| 4h | 0.403000 | 0.402625 | 0.371049 | 1,700 |
| 5h | 0.527500 | 0.522750 | 0.481000 | 1,679 |
| 6h | 0.687800 | 0.677475 | 0.626639 | 1,655 |

**Finding:** weighting by valid forecast origins does **not** reconcile the narrative values. In all six horizons, pooled values are farther below the narrative figures than the equal-event macro means. Therefore simple sample-weighted aggregation is not a sufficient explanation for the discrepancy. This does not identify the true cause; an alternate run/revision or another aggregation definition remains possible.

## Scientific significance

1. Macro mean is more interpretable for the intended generalization unit (independent flood event), although only four events remain available.
2. Pooled RMSE emphasizes events with more forecast origins; the resulting larger effective row count must not be mistaken for independent flood episodes.
3. The stored `selected_model` is chosen after viewing each outer holdout RMSE, so neither macro nor pooled selected-candidate aggregates estimate an unbiased prospective algorithm-selection procedure.
4. This analysis verifies arithmetic consistency only, not dataset hashes, model fitting, leakage, CI or real-world predictive accuracy.

## Next investigation

- Recover narrative table generation history and relevant Git commits.
- Check whether narrative values reflect a different run, feature set or source version.
- Recover original per-origin residuals and compare exact pooled and macro summaries.
- Freeze a nested model-selection experiment using the untouched outer event for final evaluation.
- Correct final academic tables only after version-level provenance is resolved; retain original records and explicit erratum.

**Source project remains read-only. No operational change was made.**
