# Critical methodological audit — Model selection on held-out flood events

**Date:** 2026-10-26. **Finding:** GA-SUL-METH-001. **Source:** private `gilliardleda2018/geoalerta-sul`, `app/ml_training.py::evaluate_event_holdout`, `scripts/evaluate_ana_events.py`, `models/evaluation/ana-events-loeo-v1.json`. Static inspection; no training rerun, no production modification.

## Finding and evidence

The code trains each candidate only on three events, evaluates the candidates on the fourth event, and then executes:

`selected = min(metrics, key=lambda item: (item.rmse_m, item.mae_m))`

where `metrics` are calculated on that **held-out event**. The resulting `selected_model` is the best observed candidate on the test event. Therefore, the selected-model aggregate reflects **post-hoc model selection using test labels**. It should not be presented as unbiased out-of-sample skill for a model-selection procedure that could have been chosen before seeing the test event.

This does **not** imply the individual candidate predictions were trained on the held-out labels: the code excludes the event from model fitting and feature admission. It does mean the *choice of the winning algorithm* used the holdout outcomes.

## Consequence for previously reported improvements

The existing report states mean selected-model RMSE of 0.0804m (1h) and 0.6878m (6h), compared with persistence 0.2833m and 1.6798m, respectively. Previously reported descriptive reductions of 71.6% and 59.1% are arithmetically correct **for those selected-model aggregates**, but can overestimate performance of a precommitted algorithm. They must be explicitly labelled *retrospective oracle-selected candidate comparison* until reanalysis.

## Scientific repair protocol

1. Freeze raw data, source commit, event list and preprocessing.
2. For each outer held-out event, use the remaining three events to perform **inner leave-one-event-out selection**, choosing a single algorithm and hyperparameters per horizon based solely on inner validation.
3. Refit the chosen algorithm on all three outer-training events.
4. Evaluate **once** on the untouched outer event. Store all candidate results for descriptive analysis, but do not select a winner using the outer event.
5. Aggregate paired per-event metrics for the fixed selection procedure, persistence, linear trend and precommitted baselines.
6. Report event-specific uncertainty and selection stability; four independent events yield limited inferential precision.
7. Keep existing results as exploratory historical evidence, not as invalid or fabricated data.
8. Require an additional forward-time or independent prospective event before making strong generalization claims.

## Related concern in July model card

`app/ml_training.py::train_models` also selects the best model and computes a 90% absolute-residual envelope using the same temporal validation segment. These quantities should be assessed on a **separate final test segment**; in-sample-on-validation residual quantiles do not establish 90% prospective interval coverage.

## Claim for prospective supervisor

“Preliminary event-wise experiments demonstrate a promising research testbed. An audit identified that the existing benchmark selects its best candidate using held-out event scores, which may introduce selection optimism. The proposed doctoral protocol therefore adopts nested event-level selection and an untouched outer test to obtain an unbiased estimate of the full model-selection procedure.”

**Status:** finding confirmed from inspected code; quantitative magnitude of selection optimism not yet measured.
