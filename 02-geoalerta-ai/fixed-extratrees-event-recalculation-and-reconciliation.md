# Independent aggregate recomputation — Fixed ExtraTrees versus post-hoc selected model

**Audit date:** 2026-10-26. **Evidence level:** arithmetic independently recomputed from serialized per-event model metrics, **not** rerun of predictions, model fitting or original datasets. Source: private `gilliardleda2018/geoalerta-sul`, `models/evaluation/ana-events-loeo-v1.json` and `app/ml_training.py::evaluate_event_holdout`.

## Purpose

The earlier leave-one-event-out report selected the lowest-RMSE model **after examining each held-out event**, introducing selection optimism. This audit separates the post-hoc event-wise oracle from one fixed algorithm, `hybrid_extra_trees`, whose recorded predictions were evaluated on each event without changing the algorithm name in response to that event's result.

## Recomputed results from four serialized event reports

Arithmetic mean of the four event-specific RMSEs (m), equally weighted by event:

| Horizon | Persistence | Linear trend 3h | Fixed hybrid ExtraTrees | Post-hoc selected (oracle) | Fixed ExtraTrees RMSE reduction vs persistence |
|---|---:|---:|---:|---:|---:|
| 1h | 0.283325 | 0.088200 | 0.081700 | 0.078525 | 71.16% |
| 2h | 0.564450 | 0.194425 | 0.175900 | 0.174300 | 68.84% |
| 3h | 0.844625 | 0.320650 | 0.293500 | 0.286975 | 65.25% |
| 4h | 1.123725 | 0.465175 | 0.408950 | 0.402625 | 63.61% |
| 5h | 1.401925 | 0.632825 | 0.537525 | 0.522750 | 61.66% |
| 6h | 1.679750 | 0.817825 | 0.697200 | 0.677475 | 58.49% |

Formula: arithmetic mean RMSE across four events; reduction = 100 × (1 − mean fixed ExtraTrees RMSE / mean persistence RMSE). These are **mean event RMSEs**, not pooled RMSE across all origins and not confidence-adjusted performance estimates.

## Important reconciliation finding

The prose `docs/EVENT_VALIDATION.md` reports selected-model means of 0.0804 m at 1h and 0.6878 m at 6h, whereas **direct arithmetic means of the four `selected_model` rows in the serialized report are 0.078525 m and 0.677475 m**. This difference requires reconciliation: potential alternate report revision, aggregation method, or transcription cannot be determined from the inspected records alone. Do **not** silently overwrite the original values or claim they are equivalent. Cite the specific source and aggregation method for every table.

## Event-specific fixed ExtraTrees RMSE

| Held-out event | 1h | 2h | 3h | 4h | 5h | 6h |
|---|---:|---:|---:|---:|---:|---:|
| 2023-09 | 0.0978 | 0.2149 | 0.3351 | 0.4784 | 0.6075 | 0.7581 |
| 2023-11 | 0.0740 | 0.1569 | 0.2563 | 0.3686 | 0.4935 | 0.6384 |
| 2024-05 | 0.0998 | 0.2107 | 0.3742 | 0.5337 | 0.6893 | 0.9226 |
| 2025-06 | 0.0552 | 0.1211 | 0.2084 | 0.2551 | 0.3598 | 0.4697 |

These values suggest fixed ExtraTrees is better than persistence on the **four reported events**, but the choice of ExtraTrees for this secondary analysis was itself informed by repository observations. This is an **exploratory fixed-candidate sensitivity analysis**, not a prospective preregistered comparison. Hyperparameter tuning and preprocessing leakage require further review.

## Scientific implications

1. Avoid presenting the original selected-model improvement as unbiased algorithm-selection skill.
2. A fixed-candidate comparison reduces the direct per-event winner-selection problem but does not remove all possible retrospective researcher selection.
3. Event-level uncertainty remains large with only four events. Report each event and avoid significance claims based on correlated quarter-hour rows.
4. Reconcile the narrative report versus JSON aggregate discrepancies before sending any final numeric claim.
5. Execute nested model selection within outer training events and test on untouched outer events for a defensible estimate of the selection procedure.
6. Preserve a complete source revision, model artifacts, data hashes, training logs and independent reproduction record.

## Submission-safe wording

“A secondary arithmetic audit of stored per-event metrics found that a fixed hybrid ExtraTrees candidate achieved lower mean RMSE than persistence in four retrospective Taquari flood holdouts. However, the analysis was selected retrospectively, and an inconsistency between the narrative summary and serialized event aggregates remains under investigation. A nested selection protocol and independent reproduction are required before stronger generalization claims.”

**No production code, model or data was changed.**
