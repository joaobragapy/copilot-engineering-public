# Case 03 — Conflicting rules

## Objective

Verify that the skill detects contradictory business requirements.

## Prompt

One supplied rule says cancelled orders must be excluded from revenue; another says all orders, including cancelled orders, must be included. Create the semantic model.

## Expected behavior

- Explicitly identifies the contradiction and its source.
- Asks the user to resolve the conflict before defining the affected metric.
- Does not silently prioritize one rule.
- Records the unresolved decision in the open-questions report.

## Verifiable criteria

- `open-questions.md` is actually created in the analysis output directory `contracts/candidates/semantic/<analysis-name>/`.
- `open-questions.md` records the contradiction between the two supplied rules: one excludes cancelled orders from revenue, and the other includes all orders, including cancelled orders. Faithful paraphrases of these rules are accepted, provided both rules are identified correctly.
- `open-questions.md` does not select either rule as the answer.
- The final response states the path of the generated `open-questions.md`.
- The generation state is stated (Generation states in `SKILL.md`). Because the prompt supplies no approved ODCS input, the run is Blocked: no `semantic-contract.yaml` is written, and the affected metric is not defined in any file.
