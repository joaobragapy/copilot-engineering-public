---
name: data-contract-authoring
description: Draft and review ODCS data contracts from explicit business and technical requirements. Use when creating, updating, or validating a data contract, identifying missing or conflicting requirements, or checking a contract against its requirements.
---

# Data Contract Authoring

## Purpose

Create reviewable data contract drafts using Open Data Contract Standard (ODCS) version `3.2.0`.

A structurally valid YAML document is not necessarily semantically correct, operationally tested, or approved.

## Interaction language

- Use Brazilian Portuguese (`pt-BR`) for user-facing communication by default, including clarifying questions, explanations, validation reports, warnings, and unresolved issues.
- Accept requirements written in Portuguese or English.
- Write contract artifacts and technical documentation in English by default.
- Preserve source field names, identifiers, controlled values, and established naming conventions when changing them could break compatibility or alter meaning.

## Required references

Before drafting or reviewing a contract, read:

- `references/odcs-guidelines.md`
- `references/architecture-decisions.md`
- `references/contract-conventions.md`
- `assets/contract-template.yaml`

If a required reference or template is missing, report it. Do not silently substitute invented project conventions.

Consult the relevant reference whenever an ODCS structure, field, logical type, allowed value, or project convention is uncertain.

## Workflow

### 1. Inspect the context

Read the supplied requirements, relevant source documentation, and any existing contract.

Identify the following only when supported by available evidence:

- Data product purpose and intended consumers.
- Producer and responsible team.
- Dataset grain.
- Fields, types, definitions, and nullability.
- Primary or composite keys and relationships.
- Quality constraints and service-level objectives.
- Security classification, retention, and access requirements.
- Operational details and ownership.

Distinguish explicit requirements, evidence-based conclusions, assumptions, and unresolved decisions.

When reviewing an existing contract, inspect its contents and status before proposing changes.

### 2. Assess completeness and consistency

Identify missing information, ambiguous terms, and contradictory requirements.

Ask focused questions when an unresolved decision materially changes the contract. If a useful draft can be produced without resolving every issue, use explicit TODOs and document the outstanding questions.

Never silently invent business rules, keys, ownership, SLAs, classifications, retention policies, or quality thresholds.

Do not infer business meaning, grain, uniqueness, or field semantics solely from field names or sample values.

### 3. Draft the contract

- Target ODCS `3.2.0` and declare `apiVersion: v3.2.0`.
- Use `assets/contract-template.yaml` as a starting point, not as evidence of business requirements.
- Follow the project conventions and the applicable ODCS requirements.
- Use structures compatible with the selected ODCS version and pinned validator.
- Keep logical types distinct from implementation-specific physical types.
- Include only supported facts and clearly identified placeholders.
- Add executable quality rules only when their meaning and thresholds are defined.
- Preserve source field names when changing them could break compatibility. Document proposed mappings separately when appropriate.
- Preserve existing approved contracts.
- Save candidate contracts to `contracts/candidates/data/`. Do not write candidates into `contracts/approved/` or other approved locations.
- Prefer a separate candidate file for material changes to an existing approved contract.
- Do not change approval status simply to make a draft appear complete.

#### Duplicate JSON properties and tabular conversion

When the source JSON contains duplicate property names:

- Preserve the original source payload unchanged in Bronze.
- Detect duplicate property names before using a parser that may silently discard earlier occurrences.
- Do not assume an ordinary key-value representation preserves every occurrence.
- Follow the duplicate-property conventions and the applicable architecture decision when defining unique, deterministic output column names.
- Preserve every occurrence or explicitly report and quarantine records that cannot be converted safely.
- Maintain the required mapping between original property names, occurrence positions, observed types, output column names, and transformation version.
- Do not infer business semantics from generated column names or type-derived suffixes.
- Do not claim lossless conversion unless it has been verified by tests.

### 4. Validate the candidate

Keep structural validation, semantic review, and data-level testing separate.

#### Structural validation

When the execution environment permits, run the configured validator against the actual candidate file:

```bash
datacontract lint path/to/contract.yaml
```

Record the actual command, exit status, and relevant output.

If the CLI is unavailable, state that validation was not executed and provide the command for the user to run.

A successful lint result supports only the checks actually performed. It does not establish business correctness, production readiness, or human approval.

#### Semantic review

Compare the candidate against the original requirements, source documentation, and applicable architecture decisions.

Check at least:

- The documented grain matches the requirements, if known.
- Field definitions and types match their supported meanings.
- Keys and relationships reflect specified identity rules.
- Constraints and quality rules are neither missing nor invented.
- Contradictions have not been silently resolved.
- Assumptions, TODOs, and unresolved decisions are explicitly reported.
- Duplicate JSON properties and conversion-related data-loss risks have been addressed when applicable.

Do not report semantic review as passed merely because structural validation passed.

#### Data-level testing

When an appropriate data source, test configuration, and authorization are available, consider:

```bash
datacontract test path/to/contract.yaml
```

Run data-level tests only when the required configuration, source, and authorization are available.

Report tests as executed only when they actually ran. Record their actual results and limitations.

Do not connect to production systems, access sensitive data, or expose credentials without appropriate authorization.

For duplicate-property conversion, test the actual parser and tabular storage implementation separately where applicable. Contract lint alone cannot establish that all source values survive conversion to Parquet.

### 5. Report the result

Return:

1. Candidate contract path.
2. Brief description of the data product and its grain, if known.
3. Structural validation status: passed, failed, or not run.
4. Semantic review findings.
5. Assumptions, TODOs, and unresolved questions.
6. Data-level tests executed and their actual results, if any.
7. Recommended next decision or action.

Keep the report concise but sufficiently detailed for a reviewer to assess the candidate.

Distinguish verified results from manual review, assumptions, and untested behavior. Never claim that a command succeeded unless it actually ran and returned a successful result.

### 6. Stop before approval or publication

A generated or modified contract remains a draft until an authorized person approves it.

Do not automatically merge, publish, deploy, promote, or overwrite approved contracts.

Instructions in this skill are workflow guidance, not technical access controls. Respect actual repository permissions and branch protection settings.

## Completion criteria

The task is complete when the candidate and its limitations are clearly reported.

A successful lint does not establish semantic correctness, successful data-level testing, or human approval.

If a material contradiction remains unresolved, report it explicitly and do not claim that the contract is ready for approval.