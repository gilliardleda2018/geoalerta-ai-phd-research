# PhD examiner readiness — GeoAlerta Sul claims, evidence and critical questions

**Version:** 1.0, 26 October 2026. **Audience:** prospective doctoral supervisor at UPC BarcelonaTech. **Status:** internal pre-submission scientific quality control, not an admission or funding assessment. **Exclusive source project:** private `gilliardleda2018/geoalerta-sul`, read-only. No original source files modified.

## 1. Executive research claim

GeoAlerta Sul is an experimental geospatial flood-decision-support implementation using multi-source hydrometeorological evidence, terrain and exposure indicators, retrospective predictive models and explicit safety gates in the Vale do Taquari. Its proposed doctoral contribution is a **falsifiable investigation** of provenance-aware integration, calibrated uncertainty, event-wise reliability and adaptation to other cities. The evidence does **not yet demonstrate** universal portability of predictive skill, official alert authority or independently reproduced end-to-end performance.

## 2. Reviewer-facing evidence matrix

| Potential examiner question | Current evidence | Scientific qualification | Missing evidence |
|---|---|---|---|
| What is the real research problem? | Taquari 2023/2024 flood literature; public SGB/ANA/Cemaden/Defesa Civil sources; source catalog | Fragmented data access and inconsistent temporal/spatial semantics are a research hypothesis to quantify, not proof of absence of prior integrated services | Measured latency, missingness, source conflict, integration baseline |
| What indicators are available? | `docs/INDICATORS.md`, `app/indicators.py`: 189 candidates, 147 available, 138 admitted; 30 spatial indicators | Snapshot counts, not 138 independently validated causal predictors | Per-feature manifest, availability time, ablation, holdout importance |
| What predictive skill has been observed? | `docs/ML_MODEL_CARD.md`: July 2026 hybrid ExtraTrees RMSE 0.0623m (1h), 0.3336m (6h) | Same-month temporal holdout; correlated origins | Frozen dataset and independent reproduction |
| Does it generalize to independent floods? | `docs/EVENT_VALIDATION.md`: four-event LOEO mean RMSE 0.0804m (1h), 0.6878m (6h); minimum recall 0.8588 at 6h | Out-of-event evaluation within the same basin, not a wholly external site or prospective validation | Additional events, prospective holdout, event-wise confidence |
| Does it improve on persistence? | LOEO report persistence RMSE 0.2833m (1h), 1.6798m (6h) vs hybrid 0.0804m and 0.6878m | Approximate relative RMSE reductions 71.62% (1h) and 59.05% (6h), computed from **reported mean metrics**; not inferential significance | Paired fold-by-fold comparison and uncertainty |
| Is spatial model improvement consistent? | EMSR720 LOAO baseline mean ROC-AUC 0.8444; enriched 0.8464; Roca Sales degrades | Enriched model fails promotion gate; all AOIs in same event and basin | New observed event and municipality/basin |
| Are event probabilities calibrated? | `docs/CRITICAL_EVENT_CALIBRATION_V2.md`: 6h Brier 0.0841 to 0.0740, 12h worsens, 24h blocked | Retrospective nested calibration; not prospective operational approval | Reliability diagrams, prevalence and independent replication |
| Are false alarms managed? | v5/v6 Stage 5 decision rejects v6 despite recall meeting minimum | Rejection arithmetic independently checked; underlying model outputs not reproduced | Real prospective shadow logs, independent review |
| Are maps of Lajeado observed truth? | Municipal simulated stage maps, 18 stages and 11,235 points | Conformance only, not observed flood-event validation | Field marks, local satellite extent and uncertainty |
| Is the architecture portable to any city? | Configurable geographic adapters, source catalog, municipal threshold and terrain layers | Architectural portability only; universal model skill **not demonstrated** | Second basin test, configuration effort and transfer degradation |
| Are copilots based on LLMs validated? | Decision-support UI and human review functions | No verified LLM agent/coplanar copilot evaluation in inspected evidence | Exact model/provider, prompts, API, tool policies, evaluation |
| What did the applicant personally build? | Applicant account of SETUT Oracle PL/SQL; repository ownership | Does not independently attribute individual authorship of GeoAlerta modules | Commit-level contribution mapping and technical statement |

## 3. Quantitative comparison and wording control

The four-event LOEO report lists mean persistence and hybrid RMSE:

| Horizon | Persistence mean RMSE (m) | Hybrid mean RMSE (m) | Descriptive relative RMSE reduction |
|---|---:|---:|---:|
| 1h | 0.2833 | 0.0804 | 71.62% |
| 6h | 1.6798 | 0.6878 | 59.05% |

Computed as `100*(1 - hybrid_mean_rmse/persistence_mean_rmse)`. These are ratios of **reported aggregate means**; they do not replace paired per-event significance tests, do not imply reductions in flood damage or deaths, and do not establish operational readiness. The reported minimum flood recall at 6h is 0.8588, which motivates safety restrictions.

## 4. Claim corrections required before submission

1. Avoid saying “all GeoAlerta algorithms are validated.” Say “selected models have repository-reported retrospective holdout evaluations.”
2. Avoid saying “the GeoAlerta model works in any city.” Say “the architecture was designed for geographic adaptation; predictive transfer remains to be tested.”
3. Avoid saying “the GeoAlerta platform replaced dispersed institutional databases.” Say “it federates evidence and harmonizes selected sources while preserving official data authority.”
4. Avoid saying “the spatial ROC-AUC proves flood prediction accuracy in Lajeado.” Distinguish upstream EMSR720 observed delineations from simulated municipal conformance.
5. Avoid presenting `ML_MODEL_CARD.md`'s heading “independent later evidence” as fully external validation: the LOEO is independent **by held-out flood event**, within the same river basin, not external geographic/prospective validation.
6. Do not claim the candidate v6 model was deployed: Stage 5 rejected it.
7. Distinguish risk-score heuristics from calibrated flood probabilities, and source ingestion from verified live availability.

## 5. Required evidence package for professor

**Document A:** 8–12 page research proposal with precise hypotheses, prior work and experimental methodology.  
**Document B:** 12–20 page GeoAlerta Sul technical research report with architecture, source provenance, model card, evaluation and threats to validity.  
**Document C:** 2-page master's dissertation summary with individual contribution; dissertation shared privately only.  
**Document D:** 3–5 page personally authored ML portfolio, commit links and reproducibility statements.  
**Appendix:** source registry, DOI bibliography, model/dataset hashes, test logs, endpoint contract matrix and selected public-source links.

These are proposed lengths, not UPC submission requirements. Obtain supervisor preferences before final formatting.

## 6. Near-term acceptance gates

- Freeze and verify exact source SHA, dependencies and data manifests.
- Reproduce at least one deterministic model gate and one statistical evaluation from authorized source data, recording commands and logs.
- Reconcile feature-count snapshots with actual artifact versions (July 189/138 vs four-event 238 candidates).
- Audit source licenses and only share public, permitted datasets.
- Attribute individual code and research contributions without speculation.
- Complete literature review with verified authors, DOI, methods, limitations and novelty comparison.
- Write a short cover letter that makes no funding or admission assumptions.

## 7. References and source classification

**Internal, not peer-reviewed:** `docs/ML_MODEL_CARD.md`, `docs/EVENT_VALIDATION.md`, `docs/INDICATORS.md`, `docs/CRITICAL_EVENT_CALIBRATION_V2.md`, `models/evaluation/topography-emsr720-loao.json`, `models/evaluation/critical-events-stage5-decision.json`, from private GeoAlerta Sul main, inspected 2026-10-26.

**Methodology:** WMO (2025), *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364, https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts ; Wilkinson et al. (2016), DOI https://doi.org/10.1038/sdata.2016.18.

**Research ethics:** No private data or source code is reproduced in this public document. Scientific claims remain conditional on verification.
