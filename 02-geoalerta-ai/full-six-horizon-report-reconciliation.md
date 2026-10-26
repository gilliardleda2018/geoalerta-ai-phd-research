# Reconciliation audit — Narrative versus serialized event benchmark

**Date:** 2026-10-26. **Source:** private `gilliardleda2018/geoalerta-sul`. **Method:** independent arithmetic on four serialized event reports, no model training or dataset-byte verification.

## Evidence identifiers

- Narrative: `docs/EVENT_VALIDATION.md`, Git blob `aea7158b7e7324cf65175f64c472978eb5b23b08`.
- Serialized: `models/evaluation/ana-events-loeo-v1.json`, Git blob `959b909840dd47ba75c64790a36dedb2337b8393`.
- Selection implementation: `app/ml_training.py::evaluate_event_holdout`, Git blob `50fa4277b25cfa9cab35e6ef88d5fa7bb5a79661`.

## Reconciliation table

All figures in metres except recall. Recalculated selected-model means are the **unweighted arithmetic means** of four event-specific selected candidate RMSE values, and worst values are their maxima.

| Horizon | Narrative mean | Recomputed mean | Narrative worst | Recomputed worst | Narrative min recall | Recomputed min recall |
|---|---:|---:|---:|---:|---:|---:|
| 1h | 0.0804 | 0.078525 | 0.0995 | 0.0998 | 0.9962 | 0.9962 |
| 2h | 0.1766 | 0.174300 | 0.2170 | 0.2149 | 0.9600 | 0.9600 |
| 3h | 0.2904 | 0.286975 | 0.3839 | 0.3701 | 0.9400 | 0.9400 |
| 4h | 0.4030 | 0.402625 | 0.5444 | 0.5337 | 0.9200 | 0.9200 |
| 5h | 0.5275 | 0.522750 | 0.7041 | 0.6893 | 0.8941 | 0.8941 |
| 6h | 0.6878 | 0.677475 | 0.9263 | 0.9226 | 0.8588 | 0.8588 |

**Interpretation:** Agreement in minimum recall suggests some common underlying information, but does not prove identical source revisions or aggregation procedures. Differences in RMSE may arise from different runs, source revisions or aggregation methods; these explanations are hypotheses, not findings.

## Critical selection caveat

The code chooses `selected_model` by minimizing RMSE on the **held-out event**. Therefore the selected-model averages are post-hoc oracle-selected retrospective values, not unbiased performance of a preregistered selection policy. The individual candidates were trained without the held-out event, but the choice of winner was made after observing its test labels.

## Mandatory actions

1. Preserve both original reports and Git object identifiers; do not rewrite either to force agreement.
2. Recover the command logs and provenance for the narrative summary; check whether it was generated from a different JSON revision.
3. Inspect event-level candidates and aggregation scripts, including weighting and rounding conventions.
4. Rerun evaluation in an isolated environment with frozen source and data; record SHA-256 of raw inputs, code revision and execution logs.
5. Use nested event-level model selection before claiming unbiased algorithm-selection skill.
6. Until resolved, label all narrative summary figures **repository-reported, not reconciled**; use recomputed JSON arithmetic only when explicitly naming the JSON and aggregation method.
7. Preserve the minimum recall decline with forecast horizon and the prohibition on autonomous warnings.

## Safe academic wording

“An independent arithmetic audit of four stored event-level reports identified discrepancies between a narrative validation summary and its serialized benchmark in mean and worst-case RMSE, while minimum reported event recall agrees. The origin of the discrepancy has not yet been established. The doctoral methodology therefore prioritizes immutable data/code provenance, nested model selection and independent reproducibility before final performance claims.”

**Status:** arithmetic verification completed; source-run reconciliation and ML reproduction pending. No source or production changes.
