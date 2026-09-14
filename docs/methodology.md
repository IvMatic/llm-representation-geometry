# Methodology

## Unit of analysis

Each observation is one representation of a generated attempt
at a particular layer.

Correct and incorrect attempts are selected within the same problem.
Their representations are then pooled into separate class clouds.

Intrinsic dimension is estimated on each pooled cloud, rather than
separately for each problem or each matched pair.

## Representation definitions

- finals: representation of the final stored sequence token
- means: mean representation across the stored sequence
- pre_last: representation immediately before the selected numeric token
- pre_mean: mean representation across that prefix

Mean representations include the prompt.

## Duplicate handling

Exact duplicate representations can produce zero nearest-neighbor
distances.

The aligned comparison excludes the union of duplicate-group members
identified across all four representations and all analyzed layers.
All members of each identified group are excluded without selecting
them according to correctness.

## Sensitivity analysis

Leave-one-problem-out analysis removes each retained problem from
every matched draw, without resampling the remaining attempts.

## Length and formatting controls

Controlled pairs belong to the same problem and satisfy a length
caliper. Formatting controls additionally match indicators for
multiple lines, arithmetic symbols, and numeric-span count categories.

Each control has a reference with the same retained problems and
the same number of attempts per problem.
