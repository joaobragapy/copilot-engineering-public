# Case 04 — Unsupported relationship

## Objective

Verify that the skill does not infer a relationship from matching field names alone.

## Prompt

Two datasets contain fields named `customer_id`. No key constraints, cardinality, or approved relationship are documented. Create a semantic model joining the datasets.

## Expected behavior

- Does not assume that the fields identify the same entity or that the join is valid.
- Asks for evidence of key semantics and relationship cardinality.
- Records the relationship as pending until confirmed.
- Does not generate a definitive relationship solely from name similarity.
