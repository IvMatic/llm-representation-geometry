# Representation Geometry of Correct and Incorrect LLM Responses

An ongoing research project extending my MSc thesis on the geometry
of transformer hidden-state representations.

## Research question

How does the intrinsic dimension of hidden-state representations
differ between correct and incorrect mathematical responses?

Does this difference persist when representations exclude the selected
numeric answer and when attempts are matched for length and formatting?

## Experimental setup

- Model: Phi-2
- Dataset: GSM8K
- Representations: full-sequence mean, final token, pre-answer mean,
  and last pre-answer token
- Intrinsic-dimension estimator: mean of local Levina–Bickel estimates
- Neighborhood sizes: k = 10 and k = 20
- Preprocessing: L2 normalization
- Comparison: correct and incorrect attempts matched within problems
- Robustness: leave-one-problem-out sensitivity analysis
- Additional diagnostics: duplicate audits and permutation references

The primary quantity is:

Delta ID = ID(incorrect) - ID(correct)

## Current findings

In the completed aligned comparison, positive intrinsic-dimension
differences persist in pre-answer representations on a common
filtered cohort.

For both pre-answer representations and both neighborhood sizes,
the mean difference remains positive at every analyzed layer under
each leave-one-problem-out omission.

Results describe an association between representation geometry
and response correctness. They do not establish a causal mechanism.

## Project status

| Analysis | Status |
|---|---|
| MSc thesis experiments | Completed |
| Aligned comparison of four representations | Completed |
| Final length and formatting controls | Completed |
| Pre-answer correctness prediction | Planned |

## Important limitations

- The aligned analysis uses a cohort filtered through a duplicate audit.
- Prefixes end before the selected numeric span; they may contain
  earlier answer mentions or answer-relevant information.
- Results depend on representation choice and neighborhood size.
- Sampling percentiles and LOPO ranges are not confidence intervals.
- Predictive generalization and causal mechanisms have not been established.

## Reproducibility

Code, environment requirements, data preparation instructions, and
selected result tables are being organized for release.

Reproduction from a fresh environment has not yet been verified.

## Background

This project builds on my MSc thesis:
*Geometric Analysis of Hidden-State Representations in Transformer
Models During Correct and Incorrect Reasoning*.
