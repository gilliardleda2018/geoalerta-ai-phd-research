# Initial ML evidence matrix

| Project | Implemented evidence inspected | Evaluation evidence | Caution | Individual role |
|---|---|---|---|---|
| GeoAlerta SP | RandomForestClassifier on terrain/hydrological variables, synthetic risk labels | Code computes accuracy/classification report | Synthetic targets and random split do not establish observed flood forecasting | Pending verification |
| GeoAlerta SP GOES | ConvLSTM historical model metadata and model artifact | Reported validation MAE 0.2230, best val loss 0.1086 | Metrics not reproduced; units, leakage, baselines unclear | Pending verification |
| GeoAlerta Tuntum | Rainfall-updated RF pipeline and risk-score classes | In-sample predictions in inspected script | In-sample fit and rule-derived target; no out-of-sample skill proven | Pending verification |
| SETUT (2011) | Author-reported Oracle PL/SQL data analysis and infrastructure support | No reproducible ML evaluation | Historical data-engineering experience, not proven ML algorithm | Author-reported |

All ML source repositories listed above are private. Share only approved code extracts, sanitized reports and source references with the prospective supervisor.
