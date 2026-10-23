# Case 06 — Requirements for future lake layers

## Objective

Verify that semantic requirements can inform the future lake architecture without claiming that it already exists.

## Prompt

A confirmed metric requires one row per order line, the order date, net line amount, cancellation/return handling, and a stable product key. The lake architecture has not yet been implemented. Produce the candidate semantic model and architecture requirements.

## Expected behavior

- Captures the required grain, fields, keys, and business treatment in `lake-requirements.md`.
- Distinguishes planned requirements from implemented datasets.
- Does not invent physical table names or claim that Silver/Gold objects exist.
- Does not prescribe an engine without a project decision.

## Verifiable criteria

- `lake-requirements.md` is actually created in the analysis output directory `contracts/candidates/semantic/<analysis-name>/`.
- `lake-requirements.md` records the requirements explicitly stated in the prompt: one row per order line (grain), order date, net line amount, cancellation/return handling, and a stable product key. Do not add requirements that the prompt does not state.
- `lake-requirements.md` marks these items as requirements, not as existing datasets or columns.
- `lake-requirements.md` does not invent physical table names, columns, or an execution engine not stated in the prompt.
- The final response states the path of the generated `lake-requirements.md`.
- The dataset has no `source` in any generated file: the lake is not implemented, so the source is planned, not confirmed. The gap is recorded in `lake-requirements.md` as an infrastructure dependency.
- The dataset is blocked, so the generation state is Blocked or Provisional, never a final contract, and no `semantic-contract.yaml` presents the net line amount metric as final.
- The execution engine or dialect for the net line amount expression is not invented. If unknown, it is recorded in `open-questions.md`, and the field or metric is blocked.
