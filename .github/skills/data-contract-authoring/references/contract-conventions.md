# Contract Conventions

These conventions define the initial project defaults for data contract authoring. They do not override ODCS requirements, source-system constraints, or explicit business decisions.

## 1. Language

- Use Brazilian Portuguese (`pt-BR`) for user-facing interaction by default, including clarifying questions, explanations, validation reports, warnings, and unresolved issues.
- Accept requirements written in Portuguese or English.
- Write data contract artifacts and technical documentation in English by default.
- Translate business descriptions into clear English without changing their meaning.
- Preserve existing source field names, identifiers, controlled values, and established naming conventions when changing them could break compatibility or alter meaning.
- Do not translate identifiers or controlled values arbitrarily.
- Ask for clarification when a translation could materially change or obscure the business meaning.

## 2. Naming

- Prefer clear, stable names that describe the business concept rather than an implementation detail when the source requirements support that choice.
- Follow the source system's established naming convention when preserving compatibility is important.
- For newly proposed names, use one consistent convention within the same contract.
- Do not mix `camelCase`, `snake_case`, and other conventions without a documented reason.
- Do not rename existing fields solely to impose a preferred style.
- Do not infer business meaning from a field name alone.

## 3. Grain and entity meaning

- Document the dataset grain whenever sufficient information is available.
- Describe grain as what one record represents, using a specific business description.
- Identify the entity or table represented by each schema definition.
- If the grain is unknown or ambiguous, ask a focused question or document a TODO.
- Do not infer the grain from field names alone.

## 4. Fields and types

- Provide a field description when its business meaning is known.
- Use logical types that reflect the meaning of the data and are supported by the selected ODCS version.
- Include physical types only when the producer or target platform provides reliable evidence.
- Document requiredness, nullability, allowed values, and constraints only when they are specified or supported by verified evidence.
- Treat identifiers as identifiers rather than quantities unless their business semantics establish otherwise.
- Preserve source types and names when compatibility is required; document proposed mappings separately when appropriate.
- Do not infer a field's meaning solely from its name or sample values.

## 5. Keys and relationships

- Declare primary keys, composite keys, uniqueness constraints, and relationships only when supported by explicit requirements or verified source evidence.
- Confirm the intended identity rule when identifiers may repeat across systems, tenants, dates, or other scopes.
- Do not assume that a field is unique simply because its name contains `id`.
- If key requirements conflict, report the conflict and do not silently select a definition.
- Distinguish a proposed key from a confirmed business rule.

## 6. Quality rules and service levels

- Do not invent thresholds for completeness, uniqueness, freshness, availability, accepted ranges, or other quality expectations.
- Record only rules whose meaning and threshold are defined by requirements or explicitly approved decisions.
- Make quality rules testable whenever the available standard and execution environment support them.
- Distinguish descriptive expectations from executable checks.
- State when a rule cannot be tested because its threshold, data source, configuration, or implementation is missing.
- Never report a quality rule as satisfied unless the relevant test was actually executed and its result supports that conclusion.

## 7. Security, privacy, and ownership

- Do not infer security classification, personal-data status, lawful basis, retention policy, access policy, or data residency requirements from a dataset name.
- Do not invent owners, stewards, contact details, or escalation paths.
- Ask for the applicable organizational standard or record the item as unresolved.
- Never include passwords, tokens, private keys, or other secrets in contracts, prompts, examples, or test fixtures.
- Follow applicable organizational and legal requirements when they are supplied.

## 8. Documentation quality

- Prefer concise, unambiguous descriptions.
- Use business terminology consistently throughout the contract.
- Define domain-specific abbreviations when the intended audience may not know them.
- Avoid claims that cannot be traced to requirements, source evidence, or an explicitly approved decision.
- Label unresolved assumptions and TODOs clearly.
- Do not use placeholder values that could be mistaken for production facts.

## 9. File and change management

- Use a separate candidate file when creating a draft or proposing a material change to an existing approved contract.
- Inspect an existing contract's contents and status before proposing modifications.
- Do not overwrite approved or protected contracts without explicit authorization and compliance with repository controls.
- Report the files created or changed.
- Report the validation commands actually executed and their results.
- Do not claim validation succeeded if the relevant command was not run.

## 10. Precedence

When conventions conflict, apply the following precedence:

1. Applicable legal, security, and organizational requirements.
2. Explicitly approved business requirements and decisions.
3. Source-system compatibility requirements.
4. The selected ODCS version and its constraints.
5. These project conventions.
6. General naming and documentation preferences.

If two requirements at the same level conflict, surface the conflict instead of resolving it silently.

## 11. Duplicate JSON properties and tabular conversion

- Preserve raw source payloads unchanged in Bronze.
- Detect duplicate JSON property names before parsing into ordinary key-value objects.
- Never assume that a JSON parser preserves duplicate properties.
- During conversion to a tabular schema, retain all property occurrences and assign unique, deterministic output column names.
- Keep the first occurrence's original name when possible. Append a type-derived suffix to subsequent occurrences, such as `Str`, `Int`, `Decimal`, `Bool`, `Date`, or `Timestamp`.
- Use an occurrence index when type-derived suffixes do not produce unique names.
- Maintain a machine-readable mapping containing the original property name, occurrence position, observed type, output column name, and transformation version.
- Do not treat generated suffixes as business semantics.
- Do not silently discard values, overwrite duplicate properties, or claim lossless conversion unless preservation has been verified.
- If a record cannot be converted without losing or ambiguously assigning values, quarantine it or retain it in a recoverable intermediate representation.
- Validate the resulting schema and Parquet output using the actual parser and execution engine selected for the project.
- Document unresolved source-schema anomalies and their impact on downstream consumers.

The type-to-suffix mapping is a project convention and must be implemented consistently. It is not a claim that the source API defines a canonical schema.

## 12. ODCS structure and validation

- Declare `apiVersion: v3.2.0` for contracts targeting the selected ODCS baseline.
- Use the ODCS schema structure accepted by the pinned validator; do not reuse legacy contract structures without verifying compatibility.
- Define `schema` for schema definitions rather than the legacy `models` structure.
- Use `logicalType: object` for structured records. For binary data, do not assume `logicalType: blob` is supported by ODCS 3.2.0; verify the selected standard and validator before choosing a representation.
- Represent `properties` as an array of property definitions, with each property declaring its `name`.
- Represent `servers` as an array. Use `servers: []` when no server is defined.
- When `description.limitations` is used, provide a string rather than a YAML list.
- Validate contract changes with the project's pinned validator and record the command and actual result.
- Treat the validator's successful result as evidence only for the checks it performed.
- Do not treat structural validity as proof of semantic correctness, confirmed grain, primary keys, requiredness, uniqueness, or business-rule validity.
- Re-run validation after material changes to the contract, template, validator version, or ODCS baseline.
- Verify schema details against the selected ODCS version and validator instead of assuming that a structure accepted by another version is compatible.