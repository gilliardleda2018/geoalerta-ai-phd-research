# Academic claim correction addendum — 2026-10-26

**Purpose:** Prevent overstatement of GeoAlerta Sul retrospective model skill in any material sent to UPC.

## Important qualification

Static inspection of `app/ml_training.py::evaluate_event_holdout` confirmed that the benchmark chooses `selected_model` by minimizing metrics calculated on the held-out flood event. Thus the selected-model performance summaries are **post-hoc test-event-selected**, not an unbiased evaluation of a preselected algorithm. This affects interpretations of the 71.6% (1h) and 59.1% (6h) descriptive reductions versus persistence. The reductions are correct arithmetic on repository-reported selected aggregates, but may contain selection optimism.

## Publication policy

- Preserve historical numerical tables with an explicit **exploratory / oracle-selected** label.
- Do not describe the selected-model LOEO comparison as fully nested or unbiased.
- Do not claim that the same algorithm would have been selected without seeing outer test labels.
- Do not present the 90% residual envelope as calibrated external coverage without independent test data.
- Perform nested event-level model selection and evaluate once on untouched outer events.
- Preserve the negative results and the model-governance rejection evidence.
- Correct the wording in consolidated submission documents before sending any package to the prospective supervisor.

## Submission status

No final dossier has been sent. The source code and production remain untouched. This addendum supersedes stronger interpretations in earlier drafts; the underlying stored results remain part of the exploratory evidence history.
