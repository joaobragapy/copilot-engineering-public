# ODCS Guidelines

## 1. Standard and baseline

- Target standard: Open Data Contract Standard (ODCS) `3.2.0`.
- Initial validator baseline: `datacontract-cli==1.2.7`.
- Declare `apiVersion: v3.2.0` when authoring contracts for this baseline.
- The selected versions are a project baseline. Compatibility claims must be supported by actual execution evidence.
- The template at `assets/contract-template.yaml` passed local structural validation on `2026-10-22` using Python `3.10.11` and `datacontract-cli==1.2.7`.
- Reported output: `Data contract is valid. Ran 1 checks.`
- This evidence applies only to the exact file version tested. It does not establish semantic correctness, data-level validity, or production readiness.
- Re-run validation after material changes to the template, contract structure, validator version, or ODCS baseline.

Official references:

- ODCS specification: https://bitol-io.github.io/open-data-contract-standard/
- Data Contract CLI documentation: https://docs.datacontract.com/
- ODCS and CLI documentation: https://docs.datacontract.com/open-data-contract-standard
- CLI commands: https://docs.datacontract.com/commands
- ODCS `3.2.0` schema (property, `required`, and `logicalTypeOptions` definitions): https://bitol-io.github.io/open-data-contract-standard/v3.2.0/
- CLI schema definition (`required` and `isNullable`): https://docs.datacontract.com/schema
- CLI FAQ (`required` and `isNullable`): https://docs.datacontract.com/faq
- CLI ODCS documentation: https://github.com/datacontract/datacontract-cli/blob/main/docs/docs/open-data-contract-standard.md

Consult the documentation corresponding to the declared standard version whenever a field, type, allowed value, or feature is uncertain. Do not assume that a feature available in a newer release is supported by ODCS `3.2.0`.

## 2. Contract authoring principles

### 2.1 Use evidence, not guesses

- Derive contract content from explicit requirements, supplied source documentation, or verified system metadata.
- Distinguish confirmed facts from assumptions and unresolved decisions.
- Do not invent business definitions, field meanings, ownership, contact details, keys, SLAs, quality thresholds, retention policies, or security classifications.
- When a material decision is missing, ask a focused question or document an explicit TODO.
- Do not treat an example or template as evidence of a business requirement.

### 2.2 Describe the data product clearly

Where the source information supports it, document:

- The data product's purpose and intended use.
- The producer and intended consumers.
- The grain: what one record represents.
- Field names, meanings, logical types, and requiredness.
- Keys, relationships, and constraints.
- Quality expectations and service-level objectives.
- Relevant operational, security, and ownership metadata.

Do not populate every possible section merely to make a contract look complete. Include only information supported by requirements and the selected ODCS version.

### 2.3 Logical and physical types

- Use logical types to express the meaning of data independently of a specific storage engine.
- Use physical types when the implementation or producer platform provides that information.
- Do not infer a physical type from a logical type without evidence about the target platform.
- Treat identifiers as identifiers, not numeric quantities, unless the source semantics establish otherwise.
- Preserve source field names when changing them could break compatibility. Propose mappings or aliases only when the target system supports them and the requirement is clear.
- `logicalType: number` is a valid ODCS `3.2.0` logical type. The logical type describes the data independently of the storage engine.
- The `logicalTypeOptions` keys documented for `integer` and `number` in the ODCS `3.2.0` schema are `minimum`, `maximum`, `exclusiveMinimum`, `exclusiveMaximum`, `multipleOf`, and `format`. The `format` option is described as bit width and signedness (Rust integer types), not as decimal precision or scale. The specification does not define decimal precision or scale options for `number`, so do not document them as if it did.
- When the source states that a value is decimal but gives no precision or scale, record that uncertainty in the field description. Do not invent precision, scale, `physicalType`, or range values.

### 2.4 Quality rules and service levels

- A quality rule must be understandable and testable.
- Do not invent numeric thresholds, accepted ranges, freshness intervals, availability targets, or completeness percentages.
- Distinguish a documented expectation from an executable test.
- State when a rule cannot yet be tested because its threshold, data source, or implementation is missing.
- Do not claim that a contract's quality requirements are satisfied unless the relevant tests were actually executed and their results support that claim.

### 2.5 Nullability and requiredness

- In ODCS `3.2.0`, `required` indicates whether an element may contain null values. Its possible values are `true` and `false`, and its default is `false` (ODCS `3.2.0` schema, property definitions).
- `required: false` permits null values. `required: true` declares that the element must not contain null values.
- Do not use `isNullable` as a substitute. The Data Contract CLI reads `required`, not `isNullable` (CLI schema documentation and CLI FAQ).
- Requiredness is a nullability declaration. The specification text quoted above does not define it as a rule about whether a JSON key must be present in an object. Do not treat it as a presence rule without further evidence.
- For a field whose source may return null, write `required: false` explicitly and document the source behaviour in the description.

## 3. ODCS structure validated locally

The following structural details were identified while adapting the template to the selected validator. They describe the structure accepted by the tested baseline and must not be generalized to other ODCS or CLI versions without verification.

- Use `schema` for data schema definitions rather than the legacy `models` structure.
- Use `logicalType: object` for structured records, as applicable.
- Logical types defined by the ODCS `3.2.0` schema are `string`, `date`, `timestamp`, `time`, `number`, `integer`, `object`, `array`, `boolean`, `map`, and `vector`. This is the list of types valid under the standard, not the list of types validated in this project.
- Logical types validated in this project are the ones used by the template and by the candidate contract that passed the pinned CLI lint (`object`, `string`, `integer`, `number`, `date`, and `timestamp`). Structural validation confirms that the template was accepted; it does not confirm that every type was tested against data.
- `blob` is not in the ODCS `3.2.0` logical type list above and is not used by the validated template. Do not use `blob` unless a verified ODCS version or validator behaviour supports it.
- Define `properties` as an array of property definitions, with each property declaring its `name`.
- Define `servers` as an array. Use `servers: []` when no server is specified.
- When using `description.limitations`, provide a string rather than a YAML list.
- Confirm every field, logical type, and allowed value against ODCS `3.2.0` and the pinned validator.
- A successful lint result does not establish semantic correctness, business-rule validity, or production readiness.

### Local validation evidence

**Target file:** `.github/skills/data-contract-authoring/assets/contract-template.yaml`

**Standard:** ODCS `3.2.0`

**Environment:**
- Python: `3.10.11`
- Validator: `datacontract-cli==1.2.7`

**Validation date:** `2026-10-22`

**Command executed:**

```bash
datacontract lint .github/skills/data-contract-authoring/assets/contract-template.yaml
```

**Observed result:** Passed.

```text
Data contract is valid. Ran 1 checks.
```

The validator reported successful structural validation against the selected baseline. This result does not establish semantic correctness, conformance to actual source data, or production readiness.

The evidence applies only to the exact file version tested. Re-run the command after material changes and report only the result actually observed.

## 4. Validation model

Validation must be reported in separate dimensions.

### Structural validation

Use the configured Data Contract CLI when available:

```bash
datacontract lint path/to/contract.yaml
```

Record the exact command, exit status, and relevant output.

Keep these dimensions separate. A successful `datacontract lint` is structural only: it checks the contract document against the schema in use, does not read the data, and does not show that real values respect `required`, types, keys, or business rules.

- Structural validation: `datacontract lint`, which checks the contract document only.
- Data-level testing: `datacontract test`, which checks properties such as presence, types, `required`, `unique`, and `logicalTypeOptions` against a data source. It is reported only when it has actually been executed.
  - In `datacontract-cli==1.2.7`, `required: true` produces a `field_required` check with a threshold of zero missing values (`engines/checks/create_checks.py`, `_missing_count_check`). This was verified by reading the installed code, not by running `datacontract test`. The check does not establish that JSON keys are present in each object, and the code read does not establish how "missing" is defined beyond the check engine's implementation.
- Semantic review: a human comparison of the contract with the requirements and source documentation.

Example for a nullable numeric field whose precision and scale are unknown:

```yaml
- name: quantity
  logicalType: number
  required: false
  description: >
    Quantity reported by the source. The source identifies the value
    as decimal, but precision and scale have not been specified.
```

This example documents the known information and the remaining uncertainty. It does not prove that the source values were tested.

### Semantic review

Compare the candidate contract against the original requirements and available evidence.

Check at least:

- The documented grain matches the requirements.
- Field definitions and types match their stated meaning.
- Keys and relationships reflect the specified identity rules.
- Constraints and quality rules are neither missing nor invented.
- No contradiction has been silently resolved.
- Unsupported assumptions and TODOs are explicitly reported.

Semantic review must not be reported as passed merely because structural validation passed.

### Data-level testing

When a suitable data source, test configuration, and authorization are available, consider:

```bash
datacontract test path/to/contract.yaml
```

Only report data-level tests as executed when they actually ran. Record the actual result and relevant limitations.

Do not connect to production systems, access sensitive data, or expose credentials without appropriate authorization.

## 5. Version and compatibility discipline

Before changing the ODCS or CLI baseline:

1. Review the relevant specification and release notes.
2. Verify the intended compatibility between the CLI version and ODCS version.
3. Run the structural and behavioral regression cases.
4. Record the tested versions, commands, results, and limitations.
5. Update the architecture decision and this reference together.

Never silently change dependency versions or claim that a baseline has been validated without execution evidence.

## 6. Template status

The accompanying `assets/contract-template.yaml` is an initial drafting aid.

- The template passed the structural validation documented in Section 3.
- Validation evidence applies only to the exact file version tested.
- Structural validation does not establish semantic correctness, completeness, compatibility with every ODCS feature, or production readiness.
- Treat any subsequently modified version as unvalidated until the relevant command has been executed successfully.
- Record validator errors and revise the template when needed.

## 7. Duplicate JSON properties and tabular conversion

ODCS contract authoring must not conceal data-loss risks introduced when source JSON is converted into a tabular schema.

- Preserve raw source payloads unchanged in Bronze.
- Detect duplicate JSON property names before parsing into ordinary key-value objects when the parser may discard earlier occurrences.
- Do not assume that a conventional JSON parser preserves duplicate properties.
- When defining the resulting tabular schema, ensure that all preserved occurrences have unique, deterministic column names.
- Treat generated suffixes as technical naming conventions, not as evidence of business meaning.
- Maintain a machine-readable mapping from each original property occurrence to its output column and transformation version.
- Do not claim lossless conversion unless preservation has been verified through tests.
- Validate the resulting contract with the pinned CLI and test the actual parsing and Parquet-conversion implementation separately.
- Keep unresolved source-schema anomalies explicit. Structural contract validation cannot resolve ambiguous source semantics.