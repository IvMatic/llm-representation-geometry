# Representation Geometry of Correct and Incorrect LLM Responses

An ongoing research project extending my MSc thesis on the geometry of transformer hidden-state representations during correct and incorrect mathematical responses.

The project combines intrinsic-dimension measurements, controlled comparisons, and linear correctness probes. Its aim is to understand what these measurements reveal—and what they do not establish—about model representations.

## Research questions

- How does representation geometry differ between correct and incorrect responses?
- Do these differences persist before the selected numeric answer span and after matching length and simple formatting features?
- How do neighborhood size and same-problem neighbors affect the measured differences?
- Can prefix representations predict correctness on held-out problems beyond simple text features?

## Experimental setup

- **Model:** Phi-2
- **Dataset:** GSM8K
- **Representations:** full-sequence mean, final stored token, prefix mean, and last prefix token
- **Intrinsic-dimension estimator:** mean of local Levina–Bickel estimates
- **Primary neighborhood sizes:** k = 10 and k = 20
- **Extended diagnostic grid:** k = 5, 8, 10, 15, 20, 30, 40
- **ID preprocessing:** row L2 normalization
- **Numerical computation:** direct Euclidean distances in float64
- **ID comparison:** correct and incorrect attempts matched within problems and pooled across problems
- **Earlier sensitivity analyses:** leave-one-problem-out (LOPO), duplicate audits, and descriptive permutation references

The primary geometric quantity is:

**Delta ID = ID(incorrect) − ID(correct)**

Positive Delta ID indicates higher estimated intrinsic dimension in the incorrect-response cloud. ID is estimated separately for the two pooled class clouds at each layer, not separately for individual matched pairs.

The probe experiment uses a separate evaluation protocol described below.

## Representation definitions

| Representation | Definition |
|---|---|
| `finals` | Hidden-state vector at the final stored sequence token |
| `means` | Mean hidden-state vector across the stored sequence |
| `pre_last` | Hidden-state vector immediately before the token overlapping the selected numeric answer span |
| `pre_mean` | Mean hidden-state vector across that prefix |

Mean representations include the prompt. The final stored token is not necessarily the numeric-answer token. Prefixes may contain intermediate numbers or earlier mentions of the answer.

## Current findings

**The positive pooled prefix ID gap survives the tested length and formatting controls, but the `pre_last` gap depends strongly on within-problem neighborhood structure.** A separate linear-probe analysis finds predictive information about correctness in prefix representations on held-out problems. These findings do not establish a causal reasoning mechanism.

### Aligned representation comparison

The aligned comparison uses a common cohort filtered through a duplicate audit across all four representations and all analyzed layers: **2,541 attempts across 129 problems**, with **397 attempts per class** in each matched draw.

Under the original neighbor-selection procedure, mean Delta ID is positive at every analyzed layer for both prefix representations and both primary k values. It remains positive under every LOPO omission.

Including the selected numeric answer span and subsequent text is therefore not necessary for the observed positive prefix gap on this cohort. This observation must be interpreted alongside the neighborhood-exclusion results below.

[View the aligned comparison results](results/aligned_comparison/README.md)

### Length and formatting controls

The analysis matches attempts within problems using generated-prefix length, prefix length plus simple formatting features, or full-continuation length and formatting. Features capture multiple lines, arithmetic symbols, and numeric-span count categories.

Each control has a reference with the same retained problems and per-problem attempt counts, sampled without its length or formatting constraints.

The prefix-length-and-formatting control retains **120 problems and 292 attempts per class**. Averaged over L24–L31 and 120 matched draws:

| Representation | k | Reference Delta ID | Controlled Delta ID |
|---|---:|---:|---:|
| `pre_mean` | 10 | 1.230 | 0.863 |
| `pre_mean` | 20 | 1.022 | 0.746 |
| `pre_last` | 10 | 5.985 | 5.460 |
| `pre_last` | 20 | 4.044 | 3.596 |

For this control, positive prefix gaps persist under every LOPO omission at all 32 layers. The reductions are descriptive, not causal estimates. The late-layer adjusted-minus-reference sampling-percentile ranges include zero and are not confidence intervals.

[View the control results, figures, and limitations](results/length_shape_control/README.md)

### Prefix correctness prediction

Linear probes at **L31** use all 2,541 retained attempts, with no further matched subsampling. Five outer folds hold out entire problems; three inner grouped folds select regularization using only outer-training data.

Hidden vectors are L2-normalized, then coordinate-standardized using training data only. Baseline features measure generated-prefix length and simple formatting. Combined predictors receive both inputs.

| Predictor | Mean test ROC-AUC | Mean test average precision |
|---|---:|---:|
| Prefix length and formatting | 0.743 | 0.389 |
| `pre_mean` | 0.771 | 0.461 |
| `pre_last` | 0.840 | 0.612 |
| `pre_last` + length and formatting | 0.840 | 0.612 |

These results support predictive accessibility of correctness-related information on held-out problems from this filtered cohort, particularly for `pre_last`. ROC-AUC is a ranking metric, not classification accuracy. The modest `pre_mean` ROC-AUC advantage over text features is less conclusive: its conditional resampling range includes zero.

Probe performance does not establish which information is used or that the language model uses the same signal to generate its answer. Predictive performance and intrinsic dimension measure different properties.

### Neighborhood-size diagnostics

The `pre_last` gap decreases as k increases under the original sampling procedure. Correct `pre_last` points have substantially more same-problem neighbors at the closest ranks than incorrect points do.

The gap becomes much smaller when each problem contributes only one attempt per class. This comparison changes sample size, within-problem multiplicity, and problem weighting together; it does not isolate their individual contributions.

The `finals` gap remains substantial across the tested sampling caps. Its approximate stability across k refers to the L24–L31 average, not every individual layer.

[View the neighborhood diagnostics](results/neighborhood_size/README.md)

### Same-problem neighbor exclusion

This follow-up keeps the original **397 query points per class and their weights fixed**, while changing neighbor eligibility. It compares original neighborhoods, exclusion of same-problem candidates, and exclusion of an equally sized random candidate set for each query.

Twenty random-exclusion repetitions are averaged within each matched draw. Averaged over L24–L31 and 120 draws:

| Representation | k | Original Delta ID | Random exclusion | Same-problem exclusion |
|---|---:|---:|---:|---:|
| `pre_last` | 10 | 7.510 | 7.479 | −2.838 |
| `pre_last` | 20 | 5.610 | 5.575 | −1.486 |
| `finals` | 10 | 6.834 | 6.836 | 5.477 |
| `finals` | 20 | 6.981 | 6.979 | 6.272 |

Same-problem exclusion raises correct `pre_last` ID much more than incorrect ID and reverses the late-layer average gap. Count-matched random exclusion leaves the gap nearly unchanged.

This supports a substantial contribution of within-problem neighborhoods to the measured `pre_last` gap. Restricted-neighbor estimates describe a modified measurement procedure; the sign reversal does not establish the ordering of an underlying “true” dimension. This experiment tested `pre_last` and `finals`, not the mean representations.

[View the exclusion experiment and results](results/neighbor_exclusion/README.md)

### Prefix text similarity and representation distance

Across 88 problems, correct–correct pairs have closer `pre_last`
representations and more similar generated-prefix text than
incorrect–incorrect pairs. Greater suffix similarity is associated
with smaller vector distances within problems.

These descriptive findings motivate endpoint and suffix controls;
they do not establish a causal reasoning mechanism.

[View the analysis, figures, and limitations](results/prefix_text_similarity/README.md)

### Endpoint and suffix controls

Notebook 20 tests whether correct–correct pairs remain closer when
endpoint token identities and measured suffix overlap are balanced
against incorrect–incorrect pairs within the same problem.

The analysis reuses Notebook 19's pair distances. It changes pair
weights, not hidden-state vectors or individual distances. Each
retained problem receives equal weight.

The strongest control balances the unordered pair of endpoint token
identities, the number of consecutive matching trailing tokens
(up to eight), and the compared suffix-window length.

This control retains **60 problems**, with **257 correct–correct
pairs and 573 incorrect–incorrect pairs** receiving positive weight.
The reference uses all original pairs from those same 60 problems.

Averaged over L24–L31:

| Measurement | Same-problem reference | After endpoint and suffix control |
|---|---:|---:|
| Correct–correct pair distance | 0.6890 | 0.7207 |
| Incorrect–incorrect pair distance | 1.1176 | 0.9535 |
| Distance gap: incorrect minus correct | 0.4286 | 0.2328 |

The mean distance gap is approximately **45.7% smaller** after
control. The adjusted mean gap remains positive at all 32 layers.
Correct pairs remain closer in **51 of the 60 problems**, using
each problem's L24–L31 average.

These results show that the geometric contrast is sensitive to
endpoint and suffix conditions, but persists on the retained cohort
after balancing those measured properties.

This is a **pair-distance analysis, not an intrinsic-dimension
estimate**. The reduction is descriptive, not a causal percentage
explained. The control matches suffix-overlap scores rather than
complete suffix wording; broader lexical similarity, length,
numeric content, and reasoning-related information may still differ.

[View the endpoint and suffix control results](results/endpoint_suffix_control/README.md)

## Repository guide

- [Methodology](docs/methodology.md)
- [Aligned comparison results](results/aligned_comparison/README.md)
- [Length and formatting controls](results/length_shape_control/README.md)
- [Neighborhood-size diagnostics](results/neighborhood_size/README.md)
- [Same-problem neighbor exclusion](results/neighbor_exclusion/README.md)
- [Prefix text similarity and representation distance](results/prefix_text_similarity/README.md)
- [Endpoint and suffix controls](results/endpoint_suffix_control/README.md)
- [Analysis notebooks](notebooks/README.md)

## Project status

| Analysis | Status |
|---|---|
| MSc thesis experiments | Completed |
| Aligned comparison of four representations | Completed |
| Length and formatting controls | Completed |
| Prefix correctness probes with feature baselines | Completed |
| Neighborhood-size and sampling-cap diagnostics | Completed |
| Same-problem neighbor exclusion | Completed |
| Explanation of the content underlying same-problem grouping | Open |
| Reproduction from a fresh environment | Not yet verified |

## Important limitations

- Findings do not establish causal mechanisms of model reasoning. Neighbor exclusion changes the measurement procedure, not model computation.
- The `pre_last` ID gap depends strongly on problem-level neighborhood structure. Its original positive value should not be interpreted as independent of problem grouping.
- The cohort was filtered through a duplicate audit. Conclusions do not automatically extend to the unfiltered attempt distribution.
- Prefixes may contain earlier answer mentions or other answer-relevant information. Correctness labels follow the existing numerical-answer extraction procedure.
- Length and formatting controls cover only selected features. Lexical content, reasoning structure, and other differences remain.
- Different controls retain different problem sets. Full-continuation controls use text generated after the prefix.
- Results depend on representation, layer, neighborhood size, and sampling design.
- Sampling percentiles and LOPO ranges are not confidence intervals. Earlier permutation references are descriptive, with no formal p-values reported. Probe resampling ranges are conditional on fitted predictions and do not capture full retraining uncertainty.
- Float64 computation cannot recover precision lost when representations were stored in float16.
- The analyses and follow-up hypotheses were developed using this dataset. Held-out-problem probe evaluation does not establish generalization to other models or datasets.

## Reproducibility

Analysis notebooks and selected result tables are being organized in this repository. The notebooks currently require representation caches and metadata stored on Google Drive; these inputs are not included here. Environment and data preparation instructions are being documented.

Protocols differ by notebook:

| Notebook | FINAL protocol |
|---|---|
| 13: aligned comparison | 120 matched draws, 80 descriptive permutation draws, LOPO |
| 15: length and formatting controls | 120 matched draws, 80 descriptive permutation draws, LOPO |
| 16: prefix probes | 5 outer grouped folds, 3 inner grouped folds, 1,000 conditional problem resamples |
| 17: neighborhood diagnostics | 120 matched draws, extended k grid and nested sampling caps |
| 18: neighbor exclusion | 120 matched draws, 20 random-exclusion repetitions per draw |
| 19: prefix text similarity | All within-class pairs from 88 problems; equal problem weighting; all 32 layers |
| 20: endpoint and suffix controls | Reuse Notebook 19 distances; three within-problem weighting controls; strongest control retains 60 problems |

The notebooks include QUICK execution modes and checkpoints. Some default to audit-only execution; follow their individual instructions. Default settings do not indicate the status of separately completed research runs. Refer to result manifests for the settings used to produce reported tables.

Reproduction from a fresh environment has not yet been verified.

## Background

This project builds on my MSc thesis:

*Geometric Analysis of Hidden-State Representations in Transformer Models During Correct and Incorrect Reasoning.*

The extension investigates how correctness-associated geometric measurements depend on representation choice, text properties, and neighborhood structure, alongside a separate evaluation of predictive information in prefix representations.
