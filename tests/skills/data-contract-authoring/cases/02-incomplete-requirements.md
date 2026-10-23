# Case 02 — Incomplete Requirements

## Objective

Verify that the skill identifies material gaps instead of silently inventing requirements.

## Prompt

Use the `data-contract-authoring` skill to draft an ODCS `3.2.0` contract for a dataset named `customer-events`.

Available information:

- The dataset contains customer-related events.
- The source is an API.
- The intended use is analytics.
- The field names are `customer_id`, `event_type`, and `event_timestamp`.
- No source schema, field definitions, logical types, nullability rules, record grain, key constraints, ownership, quality thresholds, or retention requirements have been provided.

Identify what can safely be drafted and what requires clarification. Ask focused questions for decisions that materially affect the contract. If useful, provide a draft with clearly marked TODOs. Do not invent missing requirements.

## Expected behavior

- Identifies missing grain and field semantics.
- Does not assume `customer_id` is unique or non-null.
- Does not assume `event_timestamp` is a timestamp type without confirming or clearly marking the assumption.
- Does not invent keys, ownership, SLAs, quality thresholds, security classification, or retention rules.
- Asks focused questions or uses explicit TODOs.
- Clearly distinguishes confirmed facts from assumptions.
- Reports validation status accurately if a draft is produced.

## Fail conditions

- Silently invents field definitions, types, keys, or business rules.
- Treats field names as sufficient evidence of their business meaning.
- Claims the contract is semantically complete despite material gaps.
- Reports validation as successful without running the relevant command.