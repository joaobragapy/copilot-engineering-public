---
name: semantic-contract-authoring
description: Creates or revises a candidate vendor-neutral semantic contract based on an approved ODCS data contract and business requirements expressed in natural language. Asks targeted questions when missing or contradictory information could change semantic meaning, and records unresolved items instead of inventing business rules.
---

# Semantic Contract Authoring

## Purpose

Create or revise a **candidate** semantic contract in Apache Ossie format, using the project's pinned Ossie version. The skill defines business meaning independently of a BI vendor and records requirements that the lake architecture must satisfy.

This skill does not implement Bronze, Silver, Gold, pipelines, BI models, or dashboards. It does not approve business definitions on behalf of the user.

## Required inputs

- An approved ODCS data contract, supplied as a file or a clearly identified repository path. Approval is determined as follows:
  - **Approved:** the top-level `status` is `active` and the user explicitly states in the request that the contract is approved for this analysis. Record that statement and its source in `validation-report.md`. This combination is a policy of this skill, not a requirement of the ODCS standard. In ODCS v3 (`schema/odcs-json-schema-v3.2.0.json` in `bitol-io/open-data-contract-standard`), `status` is an unconstrained string whose listed examples are `proposed`, `draft`, `active`, `deprecated`, and `retired`; the schema defines no contract-level approval field, and it does not define `active` as approval.
  - **Not approved:** `status` is `draft`, `proposed`, `deprecated`, or `retired`, or the user has not stated approval. The fixture `sales-orders.yaml` (`status: draft`) is in this state.
  - **Undetermined:** `status` is missing or unreadable, or the approval statement is ambiguous. Treat as not approved.
  - Do not infer approval from the file's location, naming, or from the presence of `schema` or `servers` entries. The ODCS format has no separate approval field.
  - Output derived from an input that is not approved is provisional. It never produces `semantic-contract.yaml` (see Generation states).
- An analytical objective or business requirement, supplied in natural language. The user may describe it directly in the prompt or provide a document.
- Access to the project's version and authoring conventions in `references/`.

Business rules may be stated in natural language. Do not require a separate structured rules file.

## Optional inputs

- Existing Ossie semantic contract to revise.
- Data samples or profiling results.
- Draft lake architecture, including planned Silver/Gold datasets, their grain, and transformation rules.
- Existing definitions of metrics, dimensions, entities, filters, and relationships.

## Workflow

1. **Locate and inspect inputs.** Read the ODCS contract and all explicitly supplied requirements. Do not assume a path or select an arbitrary contract if several candidates exist. Determine the approval state of the ODCS input as defined in Required inputs, and the generation state as defined in Generation states.
2. **Check prerequisites.** Confirm the Ossie version pinned by the project and the corresponding schema/validator reference. If the exact version-specific schema is unavailable, do not claim validation or invent a schema.
3. **Extract business intent.** Identify analytical questions, candidate datasets, fields, dimensions, metrics, grain, relationships, time basis, filters, and aggregation rules only when supported by the inputs.
4. **Find material gaps.** Ask concise questions when an ambiguity could change a metric's value, grain, inclusion/exclusion criteria, relationship cardinality, or interpretation. Group related questions. Do not ask for details already established in the supplied material.
5. **Handle incomplete requirements.** Separate confirmed definitions, unresolved semantic decisions, and technical dependencies. Generate the portions of the candidate that are supported by evidence, and block only the affected metric, dimension, relationship, or other element when possible. Do not silently choose defaults or allow an unresolved element to appear definitive. If a required schema property cannot be populated truthfully, do not fabricate a value; report the specific blocker and ask for the minimum information needed. Record unresolved semantic decisions in `open-questions.md` and physical data dependencies in `lake-requirements.md`, even when a complete semantic contract cannot yet be generated. A blocked element may be omitted from the candidate contract when the schema permits it. If the schema requires that element or its unresolved properties, do not insert placeholders or provisional definitions merely to produce a structurally valid contract; report the blocker and preserve the supported elements separately when possible.
6. **Map to Ossie.** Use only constructs supported by the pinned version. Follow `references/odcs-to-ossie-mapping.md` and `references/semantic-modeling-rules.md`. Keep vendor-specific metadata out of the core model unless an explicitly justified extension is needed.
7. **Generate candidate artifact(s).** Keep the approved ODCS input unchanged. Do not overwrite an existing approved semantic contract without explicit authorization. If a destination is unclear, ask or provide the candidate separately.
8. **Validate.** Run the configured version-specific validator if available. Report the exact command, tool/version, and actual result. Distinguish YAML/JSON parsing, schema validation, semantic review, and execution against real data. Never fabricate command output.
9. **Report lake requirements.** Identify data fields, grain, keys, history, transformations, or quality controls the future architecture must provide. Mark these as requirements, not as proof that the lake already implements them.
10. **Summarize status.** List generated artifacts, assumptions, unresolved questions, validation performed/not performed, and any incompatibilities with the ODCS contract or planned architecture.

## Non-negotiable rules

- Never invent metrics, formulas, filters, relationships, grain, or business definitions.
- Do not equate a physical column with a business dimension or metric without evidence.
- Do not infer a key or relationship solely from similar column names.
- Do not assume that a field's presence in ODCS proves the field is populated, reliable, or available in Silver/Gold.
- Do not treat structural validation as proof of business correctness or data availability.
- Do not couple the core semantic model to Power BI, Tableau, dbt, or another vendor.
- Do not change the approved ODCS contract or other protected files.
- Clearly distinguish confirmed definitions, explicit assumptions, and unresolved items.

## Expected outputs

Generate the applicable candidate artifacts in `contracts/candidates/semantic/<analysis-name>/`, where `<analysis-name>` is a short `kebab-case` identifier derived from the subject of the analysis. Artifacts:

- `semantic-contract.yaml`: candidate Ossie semantic contract.
- `validation-report.md`: validator, version, exact command, actual result, and limitations. **Required** in the Complete and Provisional states. In the Blocked state it is optional and must be a diagnostic report only: state that no `semantic-contract.yaml` was written, that structural validation is `NOT RUN`, and list the blockers. It must not describe a validation as passed.
- `open-questions.md`: unresolved business decisions and contradictions. **Required** when there is at least one material unresolved business decision or contradiction, or when a decision is needed to define a source, dialect, or approval status (for example, which existing object is authoritative).
- `lake-requirements.md`: data and transformation requirements needed to support the semantic model. **Required** when there is at least one needed field, key, dataset, or transformation that is unavailable or unverified, or a dataset whose physical source is not identified or is only planned. Use it for infrastructure and data dependencies, not for decisions the user must make. A gap that is both a dependency and a decision may be recorded in both files.

Do not create empty or speculative artifacts. When no applicable items exist for an artifact, do not create that file. If the evidence is insufficient to generate the semantic contract, do not force a complete contract; create the supporting reports that contain evidence-based information, explain the blocker, and ask the smallest useful set of questions.

When generation is partially or fully blocked:

- Generate supported candidate components and supporting reports where they can be produced truthfully. Do not create a misleadingly complete semantic contract or fabricate required values.
- Persist unresolved business decisions and contradictions in `open-questions.md`, and physical data or architecture dependencies in `lake-requirements.md`, even when no semantic contract YAML can be generated. If a report cannot be created, explicitly explain the blocker and list the unresolved items in the final response.

## Generation states

Classify each run in exactly one state before writing any file.

1. **Complete and validatable.** The ODCS input is approved, and every requested dataset, field, relationship, and metric has all its required properties confirmed under the rules below. Write `semantic-contract.yaml` and `validation-report.md`, and validate against the pinned schema. A complete candidate still requires the project's approval process before it is treated as final.
2. **Provisional (partial).** The ODCS input is approved, at least one dataset has a confirmed physical `source`, and some requested elements are blocked. Write only the supported elements to `semantic-contract.yaml`, after validating them against the pinned schema. Mark the file and `validation-report.md` as "PARTIAL - NOT A FINAL CONTRACT". Omit blocked elements; do not stub them. Write `open-questions.md` and `lake-requirements.md` where their conditions are met.
3. **Blocked.** The ODCS input is not approved, or no dataset has a confirmed physical `source`. Do not write `semantic-contract.yaml`. Write `open-questions.md` and `lake-requirements.md` where their conditions are met, optionally write the diagnostic `validation-report.md` described above, and ask the smallest useful set of questions.

A partial result is never presented as a final contract. Do not produce YAML that fails the pinned schema.

## Physical source rules

- The pinned schema requires `source` on every dataset (`Dataset.required`: `name`, `source`). Write `source` only when the input explicitly identifies a physical table, view, or query, through the ODCS contract, the user's explicit confirmation, or an approved architectural decision. Copy the identifier verbatim.
- A logical name is not a physical source. Do not copy the ODCS `schema[].name` (for example `sales_order`) into `source`.
- A planned object (a future Silver or Gold dataset that does not yet exist) is not a confirmed source. Do not write it into `source`. Block the dataset and record the planned dependency in `lake-requirements.md`.
- When no source is identified, block the dataset. Classify the gap by who can resolve it:
  - Infrastructure or data dependency (a table, view, or query that must exist or be created): record in `lake-requirements.md`.
  - Decision the user must make (for example, which of several existing objects is authoritative): record in `open-questions.md`.
  - A gap may require both records. Do not treat `lake-requirements.md` as the default location for every missing source.
- Block every relationship, field, or metric that depends on a blocked dataset or field.

## Expression rules

- The pinned schema requires `expression` on every field and metric, and `Expression.dialects` must contain at least one `dialect`/`expression` pair. Write an expression only when both the expression text and its dialect are confirmed by the input or by an explicit decision recorded in the input.
- Do not default to `ANSI_SQL` or any other dialect to satisfy the schema. If the dialect is unknown, record the question in `open-questions.md` and block the field or metric.
- If the expression text is not defined, block the field or metric. Do not write placeholders such as `REPLACE_...`.
- The pinned schema does not reject empty or placeholder strings for `source` or `expression`, because they have no minimum length or pattern. Detect them in the semantic review, and record that review in `validation-report.md`. Passing structural validation does not prove that these values are real.

## Template and illustrative values

- The starting template is `templates/semantic-contract.yaml`. It contains `REPLACE_WITH_...` placeholders and is not an approved contract. Its values are not approved semantic decisions, and filling it in does not make a candidate approved or complete. The structure of the candidate must follow the pinned schema in `references/osi-schema.json`; if the template and the schema disagree, the schema is authoritative.
- Replace a `REPLACE_WITH_...` value only when the replacement is supported under the rules above. If a value cannot be supported, block the element under Generation states. Never leave a `REPLACE_WITH_...` value in a generated artifact.
- The template's `ANSI_SQL` dialect is a placeholder, not a confirmed dialect. The agent must not copy `ANSI_SQL` (or any other template dialect) into a generated contract without explicit user confirmation of that dialect.
- Any structural example must be treated as illustrative, not as an approved semantic definition.
- Never copy placeholder values, illustrative SQL expressions, or example dialect choices into a candidate contract as if they were approved decisions.
- Replace an illustrative value only when its replacement is supported by the ODCS contract, explicit business requirements, an approved architectural decision, or an explicit user confirmation.
- If a required Ossie property cannot be populated without inventing information, ask a targeted question or report the blocker. Do not produce a misleadingly complete artifact.
- Any illustrative `ANSI_SQL` dialect or placeholder expression, for example one from a structural example, is for structural validation only. They must not be copied into a candidate contract unless supported by the intended execution context and approved requirements.

## Expression dialects

- Use only dialect identifiers supported by the project's pinned Ossie schema.
- For Ossie 0.1.1, the supported identifiers are `ANSI_SQL`, `SNOWFLAKE`, `MDX`, `TABLEAU`, `DATABRICKS`, and `MAQL`.
- Do not declare `DAX` or Power Query M as native Ossie 0.1.1 dialects.
- Select a dialect only when supported by the intended execution context or an explicit architectural decision. If the execution context is unknown and the choice affects correctness, record the question instead of selecting a default.
- Keep BI-specific implementation decisions in the appropriate downstream implementation skill. Do not force a vendor-specific expression into the vendor-neutral core model.

## Completion criteria

- A candidate is ready for delivery only when its structure complies with the pinned schema or any validation failure is explicitly reported.
- Every semantic definition must be supported by source evidence, an explicit requirement, or an approved decision.
- Unresolved material questions must be listed in `open-questions.md`; requirements for physical data availability must be listed in `lake-requirements.md`.
- If a required schema property cannot be populated truthfully, report the blocker and ask for the minimum information needed. Do not use fabricated values to make validation pass.
- A structurally valid candidate is not automatically semantically correct, approved, executable, or supported by existing lake datasets.
- A blocker affecting one semantic element must not prevent generation of unrelated, well-supported elements when those elements can be represented truthfully and consistently with the pinned schema.
- Every unresolved material business decision must be persisted in `open-questions.md`. Every required but unavailable physical field, dataset, key, or transformation must be persisted in `lake-requirements.md`.
- A candidate must not present a blocked element as a confirmed definition. If the schema requires a value that cannot be supplied truthfully, report the blocker rather than inserting a placeholder or an unapproved default.
- Explicit user confirmation may serve as a source for a business decision when the user clearly states the decision as confirmed. Record the decision and its source. Do not treat tentative answers, ambiguous responses, or hypothetical examples as approval. If the project requires a separate formal approval, preserve that requirement.