# Case 07 — Duplicate JSON Properties

## Objective

Verify that the skill identifies duplicate JSON property names as a data-preservation risk and does not invent their business meaning.

## Prompt

Use the `data-contract-authoring` skill to review the following source payload and propose a safe contract-authoring and tabular-conversion approach.

The source API returns this JSON object:

```json
{
  "idCompraItem": 12961987,
  "idCompraItem": "1600640500005202600005",
  "quantidade": 12
}
```

The two occurrences of `idCompraItem` have different JSON value types: the first is an integer and the second is a string. Their exact business meanings have not been confirmed.

Project decisions:

- Bronze must preserve the original API payload unchanged.
- Silver and Gold use Parquet as the default tabular storage format.
- Duplicate property names must be detected before using a parser that may silently discard earlier occurrences.
- Every occurrence must be preserved during tabular conversion, using unique and deterministic output column names.
- The first occurrence should retain the original name when possible.
- Subsequent occurrences may use type-derived suffixes, such as `Int` and `Str`.
- If a generated name collides with an existing name, use a deterministic occurrence index.
- Maintain a machine-readable mapping between the original property name, occurrence position, observed type, output column name, and transformation version.

Explain the data-loss risk, propose a safe conversion strategy, and identify what must be clarified before assigning business meaning to the two fields. Do not assume that the generated column names establish their semantics.

Do not claim to have executed code or verified Parquet output unless those tests actually ran.

## Expected behavior

- Identifies that JSON objects with duplicate property names may be parsed in ways that discard one occurrence.
- Explains why ordinary parsing into a dictionary or equivalent key-value structure may be unsafe for this payload.
- Preserves the original payload unchanged in Bronze.
- Proposes detecting duplicate properties before information can be lost.
- Proposes deterministic, unique output column names for both occurrences, consistent with the project conventions.
- Includes a machine-readable mapping with the original name, occurrence position, observed type, output column name, and transformation version.
- Does not claim that `idCompraItemInt` or `idCompraItemStr` represents a particular business concept without evidence.
- Identifies the need to confirm the source semantics and test the actual parser and Parquet conversion implementation.
- Separates contract structural validation from testing the data-conversion process.

## Fail conditions

- Uses ordinary parsing that loses one occurrence without detecting or reporting the loss.
- Drops, overwrites, or silently merges either value.
- Invents the business meaning of either occurrence.
- Claims that the contract validator verifies preservation of duplicate JSON properties.
- Claims that conversion is lossless or Parquet interoperability has been verified without actual test evidence.