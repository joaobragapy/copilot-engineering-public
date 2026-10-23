# ODCS → Ossie mapping rules

## Purpose

Map an approved ODCS data contract to a candidate Ossie semantic model without conflating technical data structure with business meaning. This is a policy document; exact Ossie property names and structure must conform to the pinned 0.1.1 schema.

## General rules

1. Treat ODCS as the technical contract source, not as a complete business glossary.
2. Map only information explicitly available in ODCS or separately confirmed by the user.
3. Preserve source field and dataset identifiers when appropriate; use business-friendly labels/descriptions only when supported by requirements.
4. Keep a trace from each semantic element to its source field(s), requirement, or confirmed business decision in the report. Do not add unsupported traceability properties to the Ossie core schema; use a companion report if the schema has no suitable field.
5. A type mapping is not proof of semantic role. A date field is not automatically the business reporting date; a numeric field is not automatically a metric.
6. Do not infer relationships, primary keys, uniqueness, or cardinality from naming conventions alone.
7. Do not generate a metric expression until its business definition, relevant grain, inclusion/exclusion criteria, and aggregation behavior are sufficiently clear.
8. If the target schema cannot represent a confirmed requirement, report the gap. Do not add non-standard fields to the core model as a workaround.

## Mapping decision categories

For every proposed semantic element, classify it in the companion report as one of:

- **Confirmed** — explicitly defined in an approved requirement or source contract.
- **Candidate** — plausible interpretation grounded in source evidence but not yet approved.
- **Pending** — cannot be defined safely until a question is answered.
- **Unsupported by version** — business need is clear, but the pinned Ossie version has no verified representation.

These classifications belong in the companion report unless the pinned schema explicitly supports equivalent metadata.

## Specific mapping guidance

### Datasets

Create a semantic dataset only when its analytical role and physical/logical source are identifiable. If the source will be a future Silver or Gold object, mark that source as planned in the companion report; do not imply it already exists.

### Fields and dimensions

Include fields needed for identified analytical use cases. Mark a field as a dimension only when it is intended for grouping/filtering or the business requirement establishes that role. Do not assume every ODCS property belongs in the semantic layer.

### Relationships

Require evidence for the join keys and the intended relationship. Ask when join direction, cardinality, optionality, or many-to-many behavior affects analytical correctness. Validate referenced dataset and field names against the available contract/model.

### Metrics

Require a definition, not merely a name. Confirm relevant date basis, aggregation, filters, treatment of nulls, cancellations/returns, duplicates, currency/unit, and grain when applicable. Only ask about items material to the particular metric.

### Expressions and dialects

Use only expression constructs and dialect identifiers supported by the pinned Ossie version. Do not claim a vendor-neutral expression is executable in every engine. Prefer a portable dialect only when its semantics are clear; otherwise record the dialect dependency or unresolved portability issue.

## Change control

When revising a semantic contract, summarize additions, changes, and removals. Do not silently change an existing metric's meaning or overwrite an approved artifact.
