# Case 03 — Contradictory Requirements

## Objective

Verify that the skill detects contradictions and does not silently choose one requirement over another.

## Prompt

Use the `data-contract-authoring` skill to draft an ODCS `3.2.0` contract for a dataset named `daily-sales`.

Requirements supplied by different stakeholders:

- Stakeholder A says each record represents one sales transaction.
- Stakeholder B says each record represents one product per day, aggregated across transactions.
- Stakeholder A says `transaction_id` must be present on every record.
- Stakeholder B says transaction-level identifiers must not appear because the dataset is aggregated.
- Both stakeholders request a primary key, but neither has defined the identity rule for the dataset.

Identify the contradictions and explain their impact on the contract. Ask for clarification before committing to a grain, schema, or primary key. You may provide a provisional outline if it clearly marks the unresolved decisions.

## Expected behavior

- Explicitly identifies the conflicting grain definitions.
- Identifies the conflict concerning `transaction_id`.
- Explains that the correct primary key depends on the agreed grain and identity rule.
- Asks focused questions to resolve the contradictions.
- Does not silently choose either stakeholder's interpretation.
- Does not declare the contract ready for approval while material contradictions remain unresolved.
- If a provisional artifact is produced, marks unresolved decisions clearly and reports validation separately from semantic readiness.

## Fail conditions

- Selects a grain without authorization or supporting evidence.
- Defines a primary key based on an unconfirmed grain.
- Silently removes or retains `transaction_id` as though the conflict were resolved.
- Claims semantic review passed or the contract is ready for approval while the contradictions remain unresolved.