# Semantic modeling and conversational elicitation rules

## Goal

Define reusable, business-oriented meanings for analytical consumption while keeping the model independent of any specific BI product.

## Conversational intake

Accept natural-language requirements in Portuguese or English. The user does not need to know Ossie terminology or supply a separate structured rules file.

Ask questions only when the answer can materially change a metric's value, a dimension's meaning, the grain, a relationship, or the intended analysis. Group related questions and avoid asking for information already present in the inputs.

For every question:

- State the ambiguity briefly.
- Explain the decision it affects.
- Offer alternatives only when they are plausible and neutral.
- Do not lead the user toward a preferred business definition without evidence.

## Grain

Identify the grain of each analytical dataset when supported. Examples include one row per order, order line, event, account-day, or snapshot. Never infer grain from a table name alone. If grain is unknown and affects aggregation or joins, ask.

## Metrics

Before defining a metric, determine as applicable:

- Business meaning and unit.
- Numerator/denominator or expression.
- Aggregation behavior and whether the metric is additive, semi-additive, or non-additive.
- Inclusion/exclusion rules (e.g. cancelled, refunded, pending, test records).
- Time basis and reporting period.
- Treatment of nulls, duplicates, currency, and units.
- Required grain and whether joins could multiply rows.

Do not force all questions for every metric. Ask only those relevant to the definition at hand. If information remains unresolved, do not fabricate a definitive expression.

## Dimensions and relationships

Identify dimensions from analytical requirements, not from data types alone. Clarify hierarchy and business meaning when relevant (e.g. order date versus payment date). Relationships require evidence for join keys and cardinality; similar field names are insufficient.

## Lake dependencies

Produce explicit requirements for the future architecture, such as required fields, stable keys, grain, history, deduplication, handling of late-arriving data, derived attributes, or quality checks. Do not prescribe a specific engine or storage layout unless the project has already chosen one.

## Assumptions and open questions

Separate confirmed definitions from assumptions and unresolved questions. An assumption must be explicit, minimally scoped, and easy for the user to approve or reject. Never bury assumptions inside descriptions or SQL expressions.

## Approval status

A generated candidate is not automatically approved. Approval requires the relevant business decisions to be resolved and the project's review process to be followed. Structural validation alone does not establish business correctness.
