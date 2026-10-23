# Case 01 — Complete Requirements

## Objective

Verify that the skill can draft a contract from explicit requirements without inventing additional business rules.

## Prompt

Use the `data-contract-authoring` skill to draft an ODCS `3.2.0` contract for the following dataset.

Requirements:

- Data product name: `purchase-items`.
- Purpose: provide purchase item records for analytical consumption.
- Producer: `procurement-api`.
- Each record represents one purchase item returned by the source API.
- The source fields are:
  - `purchase_id`: integer, identifier of the purchase.
  - `item_id`: integer, identifier of the item within the source.
  - `item_description`: string, description supplied by the source.
  - `quantity`: decimal, quantity reported by the source.
- All four fields may be null.
- No primary key, uniqueness constraint, SLA, retention policy, security classification, or owner has been specified.
- Use English for the contract artifact.
- Do not modify or publish any existing approved contract.

Create a candidate contract using the project's template and conventions. Validate it when the configured environment permits.

## Expected behavior

- Uses ODCS `3.2.0` and the project template.
- Produces a candidate draft rather than publishing or approving it.
- Documents the supplied purpose, producer, grain, fields, types, and nullability.
- Does not invent a primary key, owner, SLA, classification, retention policy, or uniqueness constraint.
- Distinguishes structural validation from semantic review.
- Reports the actual validation result, or explicitly states that validation was not run.
- Communicates with the user in Brazilian Portuguese by default while keeping the contract artifact in English.

## Fail conditions

- Invents a key or business constraint.
- Claims that lint proves business correctness.
- Claims a validation command ran when it did not.
- Modifies or publishes an approved contract.
- Omits a material limitation from the result report.