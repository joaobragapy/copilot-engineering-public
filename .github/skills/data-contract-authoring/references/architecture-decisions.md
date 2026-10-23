# Architecture Decisions — Data Contract Authoring

## AD-001: Use ODCS as the contract standard

**Decision:** Use Open Data Contract Standard (ODCS) `3.2.0` for the initial implementation.

**Rationale:** ODCS is the selected open standard for the lab's data contracts.

**Consequences:**
- Contracts must declare `apiVersion: v3.2.0`.
- Features from other ODCS versions must not be assumed to work.
- Any baseline change requires compatibility review and regression testing.
- The contract structure must follow the ODCS schema accepted by the pinned validator.
- Use `schema` for the contract's data schema rather than the legacy `models` structure.
- Use `logicalType: object` for structured records. For binary data, do not assume `logicalType: blob` is supported by ODCS 3.2.0; verify the selected standard and validator before choosing a representation.
- Define `properties` as an array of property definitions, with each property declaring its `name`.
- Define `servers` as an array. Use `servers: []` when no server is specified.
- When using `description.limitations`, provide a string rather than a YAML list.

**Status:** Selected. Local structural validation passed for the template version tested on 2026-10-21. Semantic validation remains pending.

## AD-002: Pin the validator version

**Decision:** Use `datacontract-cli==1.2.7` as the initial validator baseline.

**Rationale:** A pinned version makes local tests and future comparisons more reproducible.

**Consequences:**
- Keep the dependency version explicit in the project environment.
- Record the actual installed version and test results.
- Use a compatible Python version. The selected validator version requires Python `>=3.10,<3.15`.
- The initial successful local validation was performed with Python `3.10.11`.
- Run the validator against the actual contract file instead of relying solely on examples or assumptions.
- Revalidate the contract after material changes to its structure or metadata.
- Do not claim compatibility or successful validation until the intended environment has been tested.

**Validation evidence:**

On 2026-10-21, the following command completed successfully:

`datacontract lint .github/skills/data-contract-authoring/assets/contract-template.yaml`

Result: `Data contract is valid. Ran 1 checks.`

Environment: Python `3.10.11`, `datacontract-cli==1.2.7`, ODCS `3.2.0`.

This result applies to the template version tested at that time. It confirms the check reported by the validator, not semantic correctness or production readiness. Subsequent changes must be validated again.

**Status:** Selected. Initial local installation and structural validation completed.

## AD-003: Treat generated contracts as drafts

**Decision:** Generated or modified contracts remain drafts until approved by an authorized person.

**Consequences:**
- Do not automatically publish, deploy, merge, or promote a contract.
- Do not overwrite approved contracts without explicit authorization.
- Prefer a separate candidate file when proposing material changes.
- Respect actual repository permissions and branch protection rules.

**Status:** Adopted for the initial workflow.

## AD-004: Separate validation dimensions

**Decision:** Evaluate structural validity, semantic correctness, and operational behavior separately.

**Definitions:**
- **Structural validation:** Checks performed by the validator against the applicable contract specification.
- **Semantic review:** Comparison of the contract against explicit requirements and available evidence.
- **Operational behavior:** Whether the skill followed its instructions, used the appropriate references, preserved protected files, and accurately reported its actions.

**Consequences:**
- Passing one dimension does not imply passing the others.
- A successful lint does not prove that business rules are correct.
- Reports must distinguish actual test results from manual review and untested assumptions.
- Structural validation must not be reported as evidence that primary keys, record grain, requiredness, uniqueness, field semantics, or business constraints have been confirmed.

**Status:** Adopted for the initial workflow.

## AD-005: Do not silently invent missing requirements

**Decision:** Material gaps and contradictions must be made explicit.

**Consequences:**
- Ask focused questions when missing information materially changes the contract.
- Use clearly marked TODOs when a useful draft can still be produced.
- Never silently choose between contradictory business requirements.
- Do not invent ownership, keys, SLAs, security classifications, retention policies, or quality thresholds.

**Status:** Adopted for the initial workflow.

## AD-006: Keep knowledge and tests under version control

**Decision:** Version the skill, its references, the template, and behavioral acceptance cases in Git.

**Consequences:**
- Review changes to instructions and references as changes to executable project behavior.
- Re-run relevant regression cases after material changes.
- Record important changes to the baseline and their rationale.
- Keep test results and limitations explicit.

**Status:** Adopted for the initial workflow.

## AD-007: Use English for contract artifacts by default

**Decision:** Author contract metadata and technical documentation in English by default, while allowing requirements and user interaction in Portuguese or other languages.

**Consequences:**
- Translate business descriptions into clear English without changing their meaning.
- Preserve source field names and established technical identifiers when renaming them could break compatibility.
- Do not translate controlled values, identifiers, or technical terms arbitrarily.
- Ask for clarification when a translation would change or obscure the intended business meaning.

**Status:** Proposed project convention; to be documented in `contract-conventions.md`.

## AD-008: Storage formats across medallion layers

**Status:** Accepted

- Bronze preserves the original API response as raw JSON without changing its contents.
- Silver uses Parquet as the default tabular storage format.
- Gold uses Parquet as the default tabular storage format unless a documented requirement justifies another format.
- Conversion from raw JSON to a tabular format must preserve all source values or explicitly report records that cannot be converted safely.
- Storage format choices do not imply that the source payload is semantically correct or that its schema has been fully validated.
- Duplicate JSON property names must be detected before parsing methods that may silently discard earlier occurrences.

## AD-009: Duplicate JSON property names

**Status:** Accepted — implementation and regression testing pending

**Context:** The inspected Compras.gov.br Swagger response example contains two properties named `idCompraItem`, with observed values of different types: an integer and a string. Their precise business meanings have not been confirmed.

**Decision:**
- Preserve the original JSON payload without renaming or removing properties in Bronze.
- Detect duplicate property names before using a parser that may discard earlier occurrences.
- During conversion to a tabular schema, preserve every occurrence and assign deterministic, unique column names.
- Keep the first occurrence's original name when available. For subsequent occurrences, append a suffix derived from the observed data type, such as `Str` for string and `Int` for integer.
- If the generated name still collides with an existing or generated name, append a deterministic occurrence index.
- Maintain a mapping between the original property name, occurrence position, observed type, resulting column name, and transformation version.
- Do not infer business meaning from generated technical names.
- Treat type inference as a technical observation, not as a confirmed business rule.
- Reject or quarantine records when duplicate values cannot be preserved unambiguously.
- Verify behavior with automated tests before considering the implementation production-ready.

**Open questions:**
- Confirm the exact types and semantics of duplicate properties across actual API responses, not only the Swagger example.
- Define the complete mapping between parser-level types and suffixes.
- Verify compatibility with the selected ODCS version and `datacontract-cli` version.
- Test duplicate-key preservation and Parquet interoperability with the chosen implementation.
- Define and test deterministic behavior for collisions between generated names and existing source property names.