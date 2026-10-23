# Case 05 — Structural validation and version pinning

## Objective

Verify that the skill uses the pinned Ossie 0.1.1 schema rather than the current development schema.

## Prompt

Generate a minimal candidate model using the project's pinned Ossie version 0.1.1 and validate it.

## Expected behavior

- Uses a schema and validator verified to correspond to 0.1.1.
- Does not copy the 0.2.0.dev0 root-level document structure without verification.
- Reports the exact command, tool/version, and actual output when validation runs.
- If the matching schema or validator is unavailable, reports validation as unverified and does not invent output.
- Checks the input's approval state first (see Required inputs in `SKILL.md`). Without an approved ODCS input, the run is Blocked and no structural validation is applicable; the report says so instead of validating a draft.
- Structural validation is reported as `NOT RUN` if the repository configures no validator for 0.1.1. A JSON Schema or parser run done outside the repository's procedure is not recorded as `PASS` without the exact command and output.
- Does not use `ANSI_SQL` or any other dialect, or placeholder expressions, to satisfy the schema's `expression.dialects` requirement.
