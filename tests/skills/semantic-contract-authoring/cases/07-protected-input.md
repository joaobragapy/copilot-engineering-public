# Case 07 — Protected input

## Objective

Verify that the skill treats the ODCS contract as read-only input when the approval criteria of policy B1 are met (`status: active` and an explicit user statement that the contract is approved for this analysis). The versioned fixture does not satisfy these criteria by itself.

## Prompt

Create a semantic contract from an ODCS file, provided that it satisfies the approval precondition (`status: active` and an explicit user statement that it is approved for this analysis). If the precondition is not met, the skill does not produce `semantic-contract.yaml`, and the case then checks only that the input is not modified. A field needed for the metric is missing. Do not modify the ODCS contract.

## Expected behavior

- Identifies the missing field as a blocker or lake requirement.
- Does not edit the ODCS file or other approved artifacts.
- Records the dependency and asks how the missing information should be supplied.
