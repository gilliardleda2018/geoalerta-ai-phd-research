# UPC Doctoral Dossier — Source-of-Truth Editorial Reconstruction

**Date:** 2026-10-26  
**Status:** AUTHORING SPECIFICATION — not a submitted dossier  
**Scope:** This academic repository is the primary source of truth for editorial assembly. The private `gilliardleda2018/geoalerta-sul` is the technical evidence source only. The professor's four requested materials determine the editorial structure. Do not confuse a polished design with verified science.

## Why the previous PDF failed

The 7–11-page versions drastically condensed the scientific work already present in this repository. They did not adequately include the API inventory, architecture, complete source lineage, independent arithmetic audits, caveats concerning selection on the held-out event, numerical inconsistencies, full literature-to-implementation matrix, or a useful individual-contribution evidence matrix. Three repository SVG figures are text-only placeholders; `brier-calibration.svg` is a real chart. Do not describe all four as publication-grade figures.

## Definitive package and reading order

### 1. Detailed doctoral proposal — main research argument
- `01-research-proposal/doctoral-proposal-v1-en.md` — starting text, requires fuller critical literature and references
- `01-research-proposal/literature-grounded-rationale-en.md` — research motivation
- `01-research-proposal/taquari-research-framing-en.md` — real-world case and transfer hypothesis
- `01-research-proposal/nested-event-validation-study-design.md` — crucial protocol repair
- `05-literature-review/critical-literature-implementation-matrix.md` — studies versus concrete project findings
- `05-literature-review/verified-bibliography-2026-10.md` — **verify complete bibliographic metadata before final release**

### 2. GeoAlerta Sul technical and empirical research report — main scientific evidence
- `02-geoalerta-ai/geoalerta-sul-deep-audit.md` — case, technologies, boundaries
- `02-geoalerta-ai/architecture-ai-infrastructure-audit.md` — backend, AWS, ML and claimed copilots
- `02-geoalerta-ai/api-endpoint-audit.md` — static inventory, not runtime availability
- `02-geoalerta-ai/indicator-source-model-endpoint-traceability.md` — provenance and end-to-end mapping
- `02-geoalerta-ai/data-lineage-calibration-false-alarms-audit.md` — ANA/SGB source semantics
- `02-geoalerta-ai/dataset-provenance-and-hash-register.md` — recorded hashes, **not byte-verified**
- `02-geoalerta-ai/scientific-indicator-validation-chapter-en.md` — existing research chapter to expand, not replace with a short summary
- `02-geoalerta-ai/indicator-validation-and-public-datasets.md` — indicators and legal source links
- `02-geoalerta-ai/comparative-model-validation-scorecard-en.md` — independent cohorts and algorithms
- `02-geoalerta-ai/fixed-extratrees-event-recalculation-and-reconciliation.md` — fixed-candidate sensitivity
- `02-geoalerta-ai/full-six-horizon-report-reconciliation.md` — arithmetic divergence
- `02-geoalerta-ai/pooled-versus-macro-rmse-reconciliation.md` — rejected aggregation explanation
- `02-geoalerta-ai/loeo-model-selection-bias-critical-audit.md` — **required prominent methodological disclosure**
- `02-geoalerta-ai/critical-model-artifact-audit-v5-v6.md` — shadow challenger negative results
- `02-geoalerta-ai/model-governance-promotion-gates-audit.md` — safety evaluation
- `02-geoalerta-ai/spatial-indicator-mathematical-validation-audit.md` — physical score formula
- `02-geoalerta-ai/independent-municipal-event-validation-protocol.md` — stage-wise assessment
- `02-geoalerta-ai/municipal-stage-threshold-validity-audit.md` — threshold-tuning caveat
- `02-geoalerta-ai/evidence-feature-semantics-audit.md` — rainfall and datum QA
- `02-geoalerta-ai/reproducible-experimental-protocol-v1.md` — full experimental gates
- `02-geoalerta-ai/ci-run-verification-boundary.md` — no verified successful run claimed

### 3. Master's dissertation — separate requested evidence
- `03-masters-dissertation/source-verified-summary-for-cano-en.md` — preferred, crosschecked against original 75-page dissertation
- `03-masters-dissertation/setut-historical-context.md` — institutional context
- Original dissertation PDF must remain a separate private attachment; do not commit personal manuscript to public GitHub by default

### 4. Personally developed ML experience
- `04-ml-portfolio/geoalerta-sul-individual-contribution-portfolio-draft.md`
- `04-ml-portfolio/applicant-contribution-statement.md`
- Role descriptions are applicant-reported: Founder, Data Scientist, Software Developer, Technical/Research Lead. Confirm exact contributions with source commits/technical artifacts. Exclude the obsolete `initial-evidence-matrix.md` containing SP/Tuntum from the submission.

### Submission control documents
- `docs/academic-evidence-policy.md`
- `docs/scope-decision-2026-10-26.md`
- `06-academic-submission/claim-correction-addendum-2026-10-26.md`
- `06-academic-submission/phd-examiner-readiness-and-claim-audit.md`
- `06-academic-submission/final-package-readiness-index.md`

## Required figures (all must be generated from identified source numbers)

1. System-of-systems architecture: providers -> ingestion -> provenance/normalization -> features -> distinct ML/geospatial models -> governance -> APIs -> human-facing decisions. Mark planned versus implemented versus confirmed operational elements.
2. Hydrological source and forecast pipeline (ANA/SGB station provenance, unit/time/datums, event folds).
3. Forecast model comparison across six horizons (RMSE) with unambiguous **event-mean** aggregation and exploratory fixed-ExtraTrees caveat.
4. Event-by-event errors and recall, with event IDs, denominators, label definition.
5. Calibration Brier per horizon and false-positive burden; no suggestion 24h has passed gates.
6. EMSR720 geographic holdout with AOI map and ROC-AUC comparisons, including the failing Roca Sales enrichment.
7. Municipal simulated-stage precision vs recall and prevalence; distinguish map conformance from observed flooding.
8. Indicator availability/admission figure using 189 candidates, 147 available, 138 admitted, 51 rejected; classes overlap.
9. Model-governance state machine: v5 comparator -> v6 shadow -> Stage 5 failure -> Stage 6 blocked.
10. Reproducibility/evidence maturity chart, showing unverified items honestly.

**Never invent maps, execution logs, confusion matrices or visuals with unsupported values.**

## Editorial release criteria

- Every consequential metric has an immediate source path, artifact revision, cohort, aggregation method and evidence tier.
- Any study described as peer-reviewed has verified publisher metadata/DOI; public institutional pages used appropriately.
- All adverse findings and numerical discrepancies are visible in the main text, not buried in footnotes.
- The document contains genuine methodological reasoning, rather than rephrased summaries of methods.
- The package is split into four titled PDF components, optionally bound into an indexed master PDF; the dissertation original remains separate.
- Inspect every rendered page for whitespace, clipping, page breaks, legibility, figure labels and working links.
- No LLM-copilot assertion absent verified implementation; architectural portability is not cross-basin model validation.
- Original GeoAlerta Sul code remains untouched; academic draft receives versioned modifications only.
- Applicant approves final disclosure and authorship; no email is sent before submission review.

## Current blocking scientific issues

1. `evaluate_event_holdout` chooses the best model based on the outer holdout-event metrics; nested selection not yet executed.
2. Narrative mean/worst RMSE differs from serialized event JSON for all six horizons; cause unresolved.
3. Dataset SHA-256 entries are manifest-reported, not independently checked against original bytes.
4. Full ML reruns and CI status for audited revision remain unverified.
5. Cross-city predictive transfer remains to be demonstrated.
6. Portfolio requires applicant-specific commit/PR evidence.
7. Bibliography needs author/title/publisher metadata verification.

**Instruction to any future compiler/editor:** read the authoritative files in this manifest before drafting. Do not base the dossier on the short PDF or on a prior conversational summary.
