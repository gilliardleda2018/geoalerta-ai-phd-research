# Doctoral research concept note — working draft

**Provisional title:** Reliable and Explainable Geospatial Machine Learning for Urban Flood Risk Assessment Under Data Scarcity

**Question:** Can geospatial ML integrating terrain, hydrology, rainfall and remote sensing improve out-of-area and out-of-time flood-risk assessment while producing calibrated uncertainty and interpretable evidence for decisions?

**Motivation:** Existing project code demonstrates promising implementation assets but also a methodological gap between rule-derived training labels and verified hazard outcomes. This gap is an opportunity for a defensible research program, not evidence that a new method already outperforms existing approaches.

**Proposed hypotheses (not results):** (H1) independent event labels and spatiotemporal holdouts will materially change measured generalization relative to random splits; (H2) calibrated uncertainty will improve risk-sensitive decisions over uncalibrated scores; (H3) interpretable models with spatially robust features can retain useful performance under domain shift.

**Proposed methodology:** establish provenance/licensing and observed flood-event ground truth; benchmark terrain/hydrology-only heuristics, logistic regression, random forests, gradient boosting and spatiotemporal models where data support them; use spatial-block and forward-time validation; report PR-AUC, recall at operational alert budgets, calibration/Brier score, uncertainty coverage, subgroup/geographic robustness, ablations and error analysis. Establish strong baselines and reproducibility before claiming novelty.

**Expected contribution:** a reproducible geospatial risk evaluation framework and potentially new methods for uncertainty-aware decision support, conditional on literature review and experimental results.

**Status:** research direction for supervisor discussion; not a finished literature review or confirmed research fit/funding.
