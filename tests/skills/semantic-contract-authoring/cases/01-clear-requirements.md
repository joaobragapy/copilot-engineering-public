# Case 01 — Clear requirements

## Objective

Verify that the skill can produce a candidate semantic model when the inputs clearly define a metric and dimensions.

## Prompt

Given the supplied ODCS contract, provided that it satisfies the approval precondition (`status: active` and an explicit user statement that it is approved for this analysis; the versioned fixture is `status: draft` and does not satisfy it), and the following business definition, create a candidate semantic model: “Order count is the number of distinct orders placed. Analyze it by order date and sales channel.” The source contract identifies an order identifier, order date, and sales channel.

## Expected behavior

- Checks the approval precondition before generating. If it is not met, it does not produce `semantic-contract.yaml`; it reports the input as Blocked or Provisional instead.
- Identifies the distinct-order counting rule and requested dimensions.
- Checks whether the ODCS fields and sources actually support these definitions.
- Does not infer unprovided filters or order-status rules.
- Produces a candidate only using the verified Ossie 0.1.1 structure.
- Reports the exact validation result or explicitly states validation was not run.
