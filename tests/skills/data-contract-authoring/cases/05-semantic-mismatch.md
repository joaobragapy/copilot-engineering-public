# Case 05 — Semantic Mismatch

## Objective

Verify that the skill detects unsupported business claims independently of structural validation.

## Prompt

Use the `data-contract-authoring` skill to review the candidate contract against the requirements below.

Requirements:

- Data product: `purchase-items`.
- Each record represents one purchase item.
- `purchase_id` is an integer identifier of the purchase.
- `item_description` is a string containing the description supplied by the source.
- No primary key or uniqueness rule has been specified.
- The source does not establish that `purchase_id` uniquely identifies a purchase item.

Candidate contract:

```yaml
apiVersion: v3.2.0
kind: DataContract
id: purchase-items
name: Purchase Items
version: 1.0.0
status: draft

schema:
  - name: purchase_items
    logicalType: object
    description: One record represents one purchase item.
    properties:
      - name: purchase_id
        logicalType: integer
        description: Unique identifier for each purchase item.
      - name: item_description
        logicalType: string
        description: Description supplied by the source.

servers: []
```

First, run the configured structural validator against a temporary candidate file if the environment permits. Then review the candidate against the requirements, regardless of the lint result.

Do not silently alter the requirements or treat structural validity as proof of semantic correctness.

## Expected behavior

- Reports the actual structural validation result without assuming the candidate passes.
- Identifies that the description of `purchase_id` claims uniqueness at the purchase-item level without supporting evidence.
- Explains that a purchase identifier may be shared by multiple purchase items.
- Does not assume that `purchase_id` is a primary key or a unique item identifier.
- Separates structural findings from the semantic mismatch.
- Recommends correcting the unsupported description or obtaining evidence that supports the intended meaning.
- Reports any remaining uncertainty.

## Fail conditions

- Accepts the unsupported uniqueness claim without comment.
- Treats a successful lint as proof that the field description is semantically correct.
- Invents a primary key or uniqueness constraint.
- Claims a structural validation result without executing the validator.