# Representation Geometry of Correct and Incorrect LLM Responses

An ongoing research project extending my MSc thesis on the geometry
of transformer hidden-state representations.

## Research questions

How does the intrinsic dimension of hidden-state representations
differ between correct and incorrect mathematical responses?

Does this difference persist when representations exclude the selected
numeric answer span and subsequent text?

How does the difference change when attempts are matched for length
and simple formatting features?

## Experimental setup

- **Model:** Phi-2
- **Dataset:** GSM8K
- **Representations:** full-sequence mean, final stored token,
  prefix mean, and last prefix token
- **Intrinsic-dimension estimator:** mean of local Levina–Bickel estimates
- **Neighborhood sizes:** k = 10 and k = 20
- **Preprocessing:** L2 normalization
- **Numerical computation:** direct Euclidean distances in float64
- **Comparison:** correct and incorrect attempts matched within problems
- **Sensitivity analysis:** leave-one-problem-out (LOPO)
- **Additional diagnostics:** duplicate audits and descriptive
  permutation references

The primary quantity is:

**Delta ID = ID(incorrect) − ID(correct)**

Positive Delta ID indicates higher estimated intrinsic dimension
in the incorrect-response representation cloud.

ID is estimated separately for the two pooled class clouds at each
layer. It is not estimated separately for individual matched pairs.

## Representation definitions

| Representation | Definition |
|---|---|
| `finals` | Hidden-state vector at the final stored sequence token |
| `means` | Mean hidden-state vector across the stored sequence |
| `pre_last` | Hidden-state vector immediately before the token overlapping the selected numeric answer span |
| `pre_mean` | Mean hidden-state vector across that prefix |

Mean representations include the prompt.

The final stored token is not necessarily the numeric-answer token.
A prefix may contain intermediate numbers or an earlier mention of
the answer.

## Current findings

### Aligned representation comparison

The completed aligned comparison uses a common cohort filtered through
a duplicate audit across all four representations and all analyzed layers.

The cohort contains 2,541 attempts across 129 problems. Each matched
draw contains 397 attempts per class.

For both prefix representations and both neighborhood sizes, mean
Delta ID is positive at every analyzed layer and remains positive
under every leave-one-problem-out omission.

This indicates that including the selected numeric answer span and
subsequent text is not necessary for the observed positive prefix
difference on this filtered cohort.

[View the aligned comparison results](results/aligned_comparison/README.md)

### Length and formatting controls

The completed control analysis compares attempts within the same
problem under three selection rules:

1. Similar generated-prefix length.
2. Similar generated-prefix length and matching simple formatting features.
3. Similar length and formatting of the full stored generated continuation.

Formatting features indicate multiple lines, arithmetic symbols,
and numeric-span count categories. They do not capture all aspects
of style or content.

Each control has its own reference with the same retained problems
and the same number of attempts per problem, sampled without the
length or formatting constraints.

The prefix-length-and-formatting control retained **120 problems**
and **292 attempts per class**.

Across layers L24–L31, the final results were:

| Representation | k | Reference Delta ID | Controlled Delta ID |
|---|---:|---:|---:|
| `pre_mean` | 10 | 1.230 | 0.863 |
| `pre_mean` | 20 | 1.022 | 0.746 |
| `pre_last` | 10 | 5.985 | 5.460 |
| `pre_last` | 20 | 4.044 | 3.596 |

Values average layer-level Delta ID over L24–L31 within each draw,
then average across 120 matched draws.

For this control, positive prefix differences persisted under every
LOPO omission at all 32 layers for both neighborhood sizes.

The smaller mean differences are descriptive. They do not establish
a causal contribution of length or formatting. All reported
late-layer adjusted-minus-reference sampling-percentile ranges
include zero; these ranges are not confidence intervals.

[View the control results, figures, and limitations](results/length_shape_control/README.md)

## Repository guide

- [Methodology](docs/methodology.md)
- [Aligned comparison results](results/aligned_comparison/README.md)
- [Length and formatting control results](results/length_shape_control/README.md)
- [Analysis notebooks](notebooks/README.md)


### Neighborhood-size diagnostics

The prefix intrinsic-dimension gap depends strongly on neighborhood
size and within-problem sampling. Correct `pre_last` representations
have more same-problem neighbors at the closest ranks, and the gap
becomes much smaller when each problem contributes one attempt
per class.

These findings motivate a targeted test of neighborhood composition;
they do not yet establish its causal role.

[View the diagnostics and limitations](results/neighborhood_size/README.md)

## Project status

| Analysis | Status |
|---|---|
| MSc thesis experiments | Completed |
| Aligned comparison of four representations | Completed |
| Final length and formatting controls | Completed |
| Pre-answer correctness prediction | Planned |
| Reproduction from a fresh environment | Not yet verified |

## Important limitations

- Findings describe associations between representation geometry and
  correctness; they do not establish causal mechanisms.
- The aligned analysis uses a cohort filtered through a duplicate audit.
  Conclusions do not automatically extend to the unfiltered attempt
  distribution.
- Prefixes end before the selected numeric span but may contain earlier
  answer mentions or answer-relevant information.
- Length and formatting controls match only specified features.
  Lexical content, reasoning structure, and other differences remain.
- Different controls can retain different problem sets.
- Full-continuation controls use text generated after the prefix and
  are not controls based solely on information available before the answer.
- Results depend on representation choice and neighborhood size.
  Positive findings for prefix representations should not be generalized
  to every representation and layer.
- Sampling percentiles and LOPO ranges are not confidence intervals.
  Permutation references are descriptive; no formal p-values are reported.
- Float64 computation cannot recover precision lost when representations
  were originally stored in float16.
- Predictive generalization has not yet been established.

## Reproducibility

Analysis notebooks and selected result tables are available in this
repository.

The notebooks currently require representation caches and metadata
stored on Google Drive. These inputs are not included in the repository.
Environment and data preparation instructions are being documented.

Both notebooks default to QUICK mode. The length-control notebook
additionally defaults to audit-only execution with `RUN_ID=False`.

FINAL analyses use 120 matched draws and 80 descriptive permutation
draws. Checkpoints support resuming completed portions of an analysis.

The default notebook settings do not indicate the status of separately
completed research runs. Refer to the result manifests for the settings
used to produce published tables.

Reproduction from a fresh environment has not yet been verified.

## Background

This project builds on my MSc thesis:

*Geometric Analysis of Hidden-State Representations in Transformer
Models During Correct and Incorrect Reasoning.*

The extension investigates whether correctness-associated geometric
differences persist under prefix restrictions and within-problem
length and formatting controls.
