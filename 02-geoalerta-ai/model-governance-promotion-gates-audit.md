# GeoAlerta Sul — Model governance and promotion-gate audit

**Date:** 2026-10-26. **Method:** static examination of the private GeoAlerta Sul source repository, branch main. **No code execution, no AWS changes, no model promotion.** This document is for scientific audit and not an operational warning.

## 1. Evidence and exact source paths

- `config/critical_event_promotion_gates.json`: declared acceptance thresholds and operational restrictions.
- `models/evaluation/critical-events-stage5-decision.json`: stored retrospective gate outcome.
- `models/evaluation/critical-events-v6-shadow.json`: challenger selection and holdout metadata.
- `tests/test_critical_event_stage5.py`: unit-test definitions for accept/reject/mismatched populations.
- `tests/test_non_event_false_alarms.py`: event-buffer and episode aggregation test definitions.

Source code and artifacts are private; only selected aggregate observations are documented here.

## 2. Formal promotion policy

The Stage 5 policy requires (a) recall >= 0.85 **for each held-out flood**, (b) 2025 negative-holdout false-positive rate ratio <= 0.8 relative to the baseline, (c) persistent false-signal hours ratio <= 0.8 and (d) maximum false-signal episode hours ratio <= 1.0. Allowed candidate horizons: 6 and 12 hours; 24 hours blocked.

Stage 6 additionally requires >=90 prospective shadow days, >=1 independent flood event, maximum SGB data age of 2h, level network coverage >=0.50, 6h rainfall network coverage >=0.45, and human review by Defesa Civil, SGB, ANA and field team. The policy explicitly disallows automatic public warnings and states that low probability does not prove safety.

**Distinction:** a declared policy and unit tests do not establish that the corresponding settings are enabled or enforced in the deployed service. Verify deployed revision, runtime settings and actual shadow records separately.

## 3. Observed decision and mathematical interpretation

| Criterion | 6h result | 12h result | Requirement | Outcome |
|---|---:|---:|---:|---|
| Minimum held-out flood recall | 0.9583 | 0.8542 | >=0.85 | Both pass |
| Holdout FPR ratio v6/v5 | 8.7636 | 6.2535 | <=0.8 | Both fail |
| Persistent false-signal hours ratio | 8.8857 | 5.9853 | <=0.8 | Both fail |
| Maximum false episode duration ratio | 2.6986 | 2.2308 | <=1.0 | Both fail |

The stored decision is `rejected_retrospectively`; `promotion_eligible=false`; `production_change_authorized=false`; Stage 6 blocked. It is incorrect to characterize v6 as deployed, operationally approved or an improved replacement. The report explicitly calls v5 an **experimental comparison baseline, not the production incumbent**.

The v6 thresholds recorded for elevated signals (0.00276 for 6h and 0.003665 for 12h) are extremely low as raw probability cutoffs. Their operational interpretation requires examining calibration, event prevalence, threshold optimization and false alarm costs. These thresholds cannot be presented as calibrated public-warning probabilities.

## 4. Test audit: what is and is not proven

`tests/test_critical_event_stage5.py` defines three unit tests: all gates passing leads to eligibility for **prospective shadow only**; excessive FPR forces retrospective rejection; mismatched holdout populations raise an error. `tests/test_non_event_false_alarms.py` defines two tests: symmetric known-event exclusion buffer and grouping of isolated/persistent false signals. Their presence supports intentional test coverage of specific rules, but **we have not executed them**, and they do not cover the entire production system.

## 5. Scientific questions arising from the implementation

- How should persistent false alarm burden be measured across irregular sensor timestamps and varying warning policies?
- Can event-level calibration and threshold selection reduce false alarms without reducing the minimum per-event recall below a domain-approved level?
- How robust are safety gates to incomplete known-event catalogs, datum changes and unobserved non-event periods?
- How does performance change when transferred to a new municipality, river gauge or basin?
- What human-review evidence and governance are required before a shadow model can influence municipal decision support?

## 6. Reproduction checklist

1. Freeze commit and exact JSON file hashes.
2. Verify every holdout cohort and threshold-selection input is temporally independent.
3. Execute targeted tests in a disposable environment with pinned dependencies; capture complete logs.
4. Recalculate all ratios directly from raw model outputs, and compare event-level recall and false-alarm episodes.
5. Validate threshold calibration, especially at low-probability decision cutoffs, and include reliability curves and Brier decomposition.
6. Verify Stage 6 requirements in read-only AWS/CI records before making any operational claim.

## 7. Methodological framing

The design illustrates safety-constrained model selection and falsifiable promotion gates. This is a **repository-observed engineering approach**, not proof of scientific novelty. For independent verification principles see WMO (2025), *Guidelines on the Verification of Hydrological Forecasts*, WMO-No. 1364, https://wmo.int/media/news/wmo-issues-guidelines-verification-of-hydrological-forecasts. For general reproducibility and provenance see Wilkinson et al. (2016), https://doi.org/10.1038/sdata.2016.18.
