# Data Contract Authoring — Behavioral Tests

## Purpose

Verify that the `data-contract-authoring` skill follows its instructions when drafting, reviewing, and validating ODCS data contracts.

These tests evaluate behavioral compliance. They are separate from YAML linting and do not establish production readiness.

## Test execution

1. Open the repository in the configured AI coding assistant.
2. Run each case independently, using the complete prompt in its corresponding file.
3. Allow the assistant to read the skill, its required references, and the template.
4. Preserve the assistant's response and any proposed artifacts as test evidence.
5. Evaluate the response against the case's expected behavior.
6. Record each result as `PASS`, `PASS WITH CAVEAT`, `FAIL`, `BLOCKED`, or `NOT RUN`, with a brief explanation.

Do not assume that a test passed merely because the assistant claims compliance.

## Evaluation rules

- `PASS`: the case criterion is met according to the available evidence.
- `PASS WITH CAVEAT`: a positive result with an explicit limitation that prevents a full conclusion.
- `FAIL`: the observed behavior contradicted an expected criterion.
- `BLOCKED`: the execution or a sub-step was prevented, with no positive conclusion.
- `NOT RUN`: the test was not executed.

A test may fail even if the generated YAML passes structural validation.

## Cases

| Case | Behavior under test |
|---|---|
| 01 | Drafting from complete, explicit requirements |
| 02 | Handling incomplete requirements |
| 03 | Handling contradictory requirements |
| 04 | Detecting structural invalidity |
| 05 | Detecting semantic mismatch |
| 06 | Protecting approved contracts |
| 07 | Preserving duplicate JSON properties |

## Results

Record execution results in the following format:

| Case | Result | Evidence / notes |
|---|---|---|
| 01 | PASS | Consolidated in `results/initial-evaluation.md` section 6. |
| 02 | PASS | Draft with open items generated and validated. |
| 03 | PASS | Granularity conflict identified; generation stopped with no artifacts. |
| 04 | PASS | Invalid candidate rejected by lint. |
| 05 | PASS WITH CAVEAT | Structural validation passed in the existing environment. Semantic caveat: the `purchase_id` description claims uniqueness without sufficient evidence. |
| 06 | PASS WITH CAVEAT | Candidate generated and approved contract protected. Integrity was compared with the `HEAD` blob, not with a hash reference defined in advance by the test. |
| 07 | PASS WITH CAVEAT | Authoring: PASS WITH CAVEAT. Parquet conversion: BLOCKED, not executed. The JSON has duplicate key `idCompraItem`; the standard `json.loads` drops one occurrence, so data preservation is not demonstrated. |

## Regression policy

Re-run relevant cases after material changes to the skill, references, template, validator baseline, or contract conventions.

Do not describe the skill as fully validated until the applicable test cases have been executed and their results reviewed.