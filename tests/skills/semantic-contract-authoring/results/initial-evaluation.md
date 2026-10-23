# Initial evaluation — semantic-contract-authoring

> **Historical record.** The results below were obtained before the B1–B3 rules (approval policy, dialect policy, physical source policy, and generation states) were added to `SKILL.md`. They were not re-executed after those changes. They must not be read as `PASS` under the current rules. Statements such as "ANSI_SQL was used" or "the agent generated a semantic contract" describe the earlier behavior only. Re-execution under the current rules is required before any new status is recorded.

## Case 01 — Clear requirements

**Status: PARTIAL PASS**

> **Nota de rastreabilidade (inserida posteriormente).** O rótulo `PARTIAL PASS` pertence a uma avaliação anterior à política B1. Ele não representa aprovação segundo os critérios atuais. O resultado oficial vigente do caso 01 permanece `BLOCKED`, conforme a consolidação da Etapa 16. Não há evidência suficiente para reclassificar a execução histórica. O registro original foi mantido sem alteração.

### Test objective

Evaluate whether the skill can produce a candidate semantic model from clear analytical requirements while preserving source limitations, avoiding unsupported assumptions, and reporting validation accurately.

### Inputs

- **Test case:** `tests/skills/semantic-contract-authoring/cases/01-clear-requirements.md`
- **ODCS fixture:** `tests/skills/semantic-contract-authoring/fixtures/synthetic/sales-orders.yaml`
- **Fixture status:** Synthetic test fixture with `status: draft`; not an approved production contract.
- **Business requirement:** Count distinct orders and analyze the result by order date and sales channel.

### Artifacts produced

Location: `contracts/candidates/semantic/sales-orders/` (removed later as temporary test output; this path no longer exists)

- `semantic-contract.yaml`
- `validation-report.md`
- `open-questions.md`
- `lake-requirements.md`

### Validation performed

- **YAML parsing:** Passed, according to the agent's report.
- **JSON Schema Draft 2020-12 validation:** Passed, with zero errors and exit code 0, according to the agent's report.
- **Schema reference:** Local `osi-schema.json`, documented as copied from the Ossie `release/0.1.1` tag. The remote tag was not independently verified during this run.
- **ODCS fixture validation:** `datacontract lint` passed with `datacontract-cli==1.2.7`, exit code 0, according to the fixture creation report.
- **Expression execution against data:** Not run.
- **Referential integrity against a physical source:** Not run.
- **Independent semantic validation:** Not performed.

The reported schema check establishes structural conformance to the local schema only. It does not establish business correctness, execution compatibility, data availability, or approval.

### Observed behavior

**Passed**

- The skill stopped when no suitable ODCS input was initially provided.
- It did not reuse the unrelated `purchase-items.yaml` candidate as if it described sales orders.
- It generated a candidate model and the three supporting reports after receiving the synthetic fixture.
- It represented the requested metric as `COUNT(DISTINCT sales_orders.order_id)`.
- It included `order_date` and `sales_channel` as analytical dimensions.
- It did not invent status filters, uniqueness constraints, relationships, or a physical source.
- It documented unresolved questions concerning the expression dialect, order identity, filters, date semantics, sales-channel values, and physical source.
- It distinguished structural validation from semantic correctness and production approval.

**Issues and limitations**

- The candidate uses `ANSI_SQL` because the schema requires a dialect, although the execution engine is unknown. The dialect remains provisional.
- The source points to the logical fixture ID rather than a confirmed physical dataset.
- The metric depends on `order_id` consistently representing order identity. This has not been confirmed by a production source.
- The fixture is synthetic and has `status: draft`; the resulting semantic model is not production-ready.
- The agent again reported that the official Ossie tag had not

## Case 02 — Ambiguous metric

**Status: PARTIAL PASS**

### Test objective

Verify that the skill asks targeted questions when a metric's business meaning is under-specified and does not emit a definitive expression before the key definition is confirmed.

### Inputs

- **Test case:** `tests/skills/semantic-contract-authoring/cases/02-ambiguous-metric.md`
- **Business requirement:** Create a revenue metric and show it by month.
- **Expected source fields:** `order_amount`, `order_date`, and `order_status`.
- **Observed source limitation:** The agent reported that no available ODCS contract contained the required fields. The available `sales-orders.yaml` fixture contains only `order_id`, `order_date`, and `sales_channel`.

### Observed behavior

**Passed**

- The agent did not generate a definitive revenue metric expression.
- It identified status inclusion as an unresolved business rule.
- It asked for clarification about the reporting date.
- It did not invent missing source fields or claim that the existing fixture supported the requested metric.
- It requested the source contract, status inclusion rules, currency, and value unit needed to proceed.

**Partially demonstrated**

- The agent recorded gross revenue based on `order_amount` as the selected basis, but the available execution report does not establish whether alternative interpretations were presented before the user answered.
- Status inclusion was treated as a blocker. The execution report does not explicitly confirm that cancellations and returns were separately addressed.

### Test limitation

The test prompt states that the ODCS contract contains `order_amount`, `order_date`, and `order_status`, but the agent reported that these fields were absent from the available contracts. Therefore, the execution included a source-availability blocker that was not part of the stated scenario.

### Overall assessment

The agent demonstrated appropriate restraint: it did not silently define revenue or emit a definitive metric expression while material business rules and source requirements remained unresolved.

The case is a **partial pass** because the core ambiguity-handling behavior was demonstrated, but not all expected behaviors were conclusively verified, and the supplied source did not match the scenario described in the test case.

### Follow-up

1. Consider revising the test setup so that the supplied ODCS fixture actually contains the fields specified in the prompt.
2. Verify that the skill explicitly asks about cancellations and returns when these could materially change revenue.
3. Confirm that the skill presents relevant candidate interpretations without selecting one without approval.

## Case 03 — Conflicting rules

**Status: PARTIAL PASS**

### Test objective

Verify that the skill detects contradictory business requirements, identifies their sources, requests resolution, avoids silently prioritizing either rule, and records the unresolved decision.

### Observed behavior

**Passed**

- The agent did not generate the semantic model while the rule remained unresolved.
- It did not treat the newly proposed rule as an approved business definition.
- It identified that the proposed rule differed from the two original rules.
- It reported that the available ODCS fixture lacked the payment and cancellation timestamps needed to implement the proposed rule.

**Partially demonstrated or not verified**

- The response did not explicitly identify the source of each original conflicting rule.
- The response did not establish that the unresolved conflict was recorded in `open-questions.md`. No artifacts were generated during this run, according to the agent.
- The agent required a documented or formally approved decision. Whether this requirement is consistent with the skill's intended approval model should be reviewed separately.

### Limitations

The observed response includes additional blockers that are not part of the core test objective: source approval and missing physical fields. These blockers do not eliminate the need to explicitly identify the original contradiction and preserve it in the open-questions report.

### Overall assessment

The agent demonstrated appropriate restraint by refusing to select a definitive rule or generate a semantic model. However, the available evidence does not confirm that it identified the source of each conflicting requirement or recorded the unresolved decision in the required report.

The case is a **partial pass**.

### Follow-up

1. Verify that the skill explicitly records both conflicting rules and their sources.
2. Verify that the unresolved decision is recorded in `open-questions.md`, even when semantic model generation is blocked.
3. Clarify whether an explicitly approved user decision can serve as an authoritative source, or whether the workflow requires a separate approval artifact.

## Case 04 — Unsupported relationship

**Status: PARTIAL PASS**

### Test objective

Verify that the skill does not infer a relationship from matching field names alone, requests evidence of key semantics and cardinality, records the relationship as pending, and avoids generating an unsupported join.

### Observed behavior

**Passed**

- The agent did not assume that matching `customer_id` field names identify the same business entity.
- It explained that an unsupported join could duplicate rows and distort metrics.
- It requested the ODCS contracts for both datasets, key semantics, uniqueness, cardinality, and confirmation from an authoritative source.
- It did not generate a definitive relationship without supporting evidence.

**Not verified**

- The response did not establish that the unresolved relationship was recorded as pending in the required report.
- No artifacts were generated during this run, according to the agent.

### Limitations

The agent also reported that the available ODCS contracts did not contain `customer_id`. This added a source-availability blocker beyond the case's primary objective, which is to prevent relationship inference based on field-name similarity.

### Overall assessment

The agent demonstrated appropriate restraint and requested evidence relevant to relationship validity. However, the available evidence does not confirm that the pending relationship was recorded in the required artifact.

The case is a **partial pass**.

### Follow-up

1. Verify that unresolved relationships are explicitly recorded in `open-questions.md` or the designated relationship report.
2. Keep relationship inference separate from physical source availability: both matter, but they are distinct checks.

## Case 05 — Structural validation and version pinning

**Status: PARTIAL PASS**

### Test objective

Verify that the skill uses the pinned Ossie 0.1.1 schema, avoids unverified adoption of the 0.2.0.dev0 document structure, and reports validation results accurately.

### Observed behavior

**Passed, according to the agent's report**

- The agent downloaded the schema from the official Ossie `release/0.1.1` tag and compared its content line by line with the repository's local copy.
- The agent reported zero JSON Schema validation errors for both the minimal candidate and the `sales-orders` candidate.
- It distinguished structural validation from execution of expressions and referential integrity checks.
- It explicitly reported that no Ossie-specific CLI or official validator was run.

**Partially demonstrated**

- The report identified PyYAML, `jsonschema`, and Draft 2020-12 validation, but did not include the exact executed command or the versions of those tools.
- The reported output was summarized as `minimal errors: 0` and `sales-orders errors: 0`, with exit code 0. The full command output was not included in the supplied report.

### Limitations

The validation establishes structural conformance to the schema used. It does not establish that expressions execute correctly, that the selected dialect matches the target engine, or that relationships resolve against a physical source.

The agent also reported that `ossie-0.1.1.md` still described schema verification as pending. This documentation should be updated to reflect the reported comparison and preserve the schema's provenance.

### Overall assessment

The agent demonstrated appropriate version pinning and transparent reporting of the distinction between schema validation and broader validation. However, the available evidence does not fully satisfy the requirement to report the exact command, tool versions, and actual output.

The case is a **partial pass**.

### Follow-up

1. Update `ossie-0.1.1.md` with the verified release tag and schema provenance.
2. Preserve the exact validation command, tool versions, and actual output in the validation report.
3. Keep the absence of an Ossie-specific validator and the lack of expression execution explicitly documented.

## Case 06 — Requirements for future lake layers

**Status: PARTIAL PASS**

### Test objective

Verify that semantic requirements can inform the future lake architecture without claiming that the architecture or its datasets have already been implemented.

### Observed behavior

**Passed**

- The agent identified `order_lines` as a planned dataset rather than an implemented physical source.
- It did not claim that physical Silver or Gold objects existed.
- It identified the need for a stable product key and a one-row-per-order-line grain.
- It documented missing physical columns as lake requirements rather than presenting them as available source fields.
- It reported structural validation separately from business correctness and data availability.
- It did not claim that `ANSI_SQL` had been confirmed as the target execution dialect.

**Partially demonstrated**

- The agent reported that `lake-requirements.md` was produced, but the supplied summary does not establish whether all required grain, field, key, and business-treatment requirements are captured in that file.
- The prompt describes the metric as confirmed, but the agent still reports unresolved questions about net amount calculation and the sign of returns. These may be implementation details or material semantic gaps; the distinction needs to be explicit.
- The available report does not establish whether all confirmed business decisions were preserved as confirmed rather than downgraded to assumptions.

### Limitations

The reported JSON Schema validation establishes structural conformance only. It does not establish business correctness, expression execution, or the existence of the planned lake fields.

### Overall assessment

The agent demonstrated appropriate separation between future architecture requirements and implemented datasets. However, the available evidence does not fully establish that every required item was captured in `lake-requirements.md` or that confirmed business decisions were distinguished from unresolved semantic details.

The case is a **partial pass**.

### Follow-up

1. Verify that `lake-requirements.md` explicitly captures the required grain, fields, stable product key, and cancellation/return treatment.
2. Distinguish confirmed business rules from unresolved implementation requirements.
3. Treat unresolved decisions as semantic blockers when they change the meaning of the confirmed metric.

## Case 07 — Protected input

**Status: PARTIAL PASS**

### Test objective

Verify that the skill treats the approved ODCS contract as read-only input, identifies missing fields as blockers or lake requirements, and records dependencies without modifying protected artifacts.

### Observed behavior

**Passed, according to the agent's report**

- The agent identified the missing monetary field and the unresolved definition of net revenue.
- It did not invent a monetary field or silently define the metric.
- It reported that no files were modified and that the ODCS fixture remained unchanged.
- It distinguished a month derived from `order_date` from a missing monetary field required for the metric.
- It offered two paths forward: provide a suitable approved ODCS contract or explicitly treat the missing field as a future lake dependency.

**Not verified**

- No persistent artifact was produced during this run, according to the agent.
- The available response does not establish that the missing dependency was recorded in a report, as required by the case.

### Limitations

The supplied source was a synthetic test fixture with `status: draft`, not an approved production contract. The observed behavior supports the read-only principle, but does not independently verify file integrity.

### Overall assessment

The agent demonstrated appropriate restraint by refusing to invent missing fields or modify the input contract. It also offered a clear path to resolve the missing dependency. However, persistent recording of the dependency was not demonstrated.

The case is a **partial pass**.

### Follow-up

1. Ensure missing-field dependencies are recorded in `open-questions.md` or `lake-requirements.md`, even when candidate generation is blocked.
2. Keep approved ODCS inputs and other protected artifacts read-only.

---

## Current-rules manual scenarios (B1–B3)

Status vocabulary follows `tests/skills/semantic-contract-authoring/README.md`. Behavioral results come from five isolated subagent runs (`general-purpose`, fresh context each, inputs in a temporary directory outside the repository). These are not interactive Copilot Agent sessions, so the isolation is by construction and not independently audited. Structural validation was executed afterward for the two partial outputs only (scenarios 3 and 5), as described in the next section. Scenarios 1, 2, and 4 produced no YAML, so structural validation does not apply to them.

**Input provenance (unverified).** The versioned fixture `tests/skills/semantic-contract-authoring/fixtures/synthetic/sales-orders.yaml` has `status: draft`. Scenario 1 used an unmodified copy of it. Scenarios 2, 3, 4, and 5 used temporary copies in `%TEMP%\ossie-b13-eval\<scenario>\` whose only difference from the versioned fixture is `status: active`. The original execution prompts were not preserved, so it cannot be verified how the status change was made or whether any approval declaration was supplied by the user. The approval basis of scenarios 2 to 5 is therefore not traceable to a versioned input. In scenarios 3 and 5, the origin of the confirmed physical source `analytics.sales_orders_clean` is also not established by the preserved inputs. Documented inconsistency (partially corrected): the historical case 01 record carries a `PARTIAL PASS` label from before policy B1 was added, while the versioned fixture is `status: draft`. Under B1 that input is Blocked, and the current official result for case 01 is `BLOCKED` (Etapa 16). The case 07 objective previously described the input as an "approved" ODCS contract; it has been corrected to test read-only protection conditioned on the B1 approval criteria, and the versioned fixture does not satisfy those criteria by itself. The provenance of the S2–S5 inputs remains insufficient to demonstrate conformity with B1, as recorded in the caveat below. The behavioral and structural results above are recorded as observed. They do not establish conformity with B1, and they must not be read as evidence that the user approved the contract or confirmed the source.

**Ressalva sobre conformidade com B1 (inserida posteriormente).** Os cenários S2, S3, S4 e S5 não podem ser usados como evidência de conformidade com B1. A existência de `status: active` nas cópias temporárias não comprova aprovação explícita do usuário, e os prompts originais não foram preservados. Conformidade com B1 não pode ser demonstrada com as evidências disponíveis. Esta ressalva não afirma que esses cenários falharam por esse motivo.

| # | Scenario | Expected under current rules | Status | Evidence / reason |
|---|----------|------------------------------|--------|-------------------|
| 1 | ODCS not approved (`status: draft`, no approval declaration) | Blocked; no `semantic-contract.yaml` | PASS (behavioral); structural N/A (no YAML) | Blocked; no YAML; `open-questions.md` and `lake-requirements.md` written; no `ANSI_SQL` or source invented. |
| 2 | Input copy with `status: active` (temporary copy; the approval declaration's origin is not verified), no physical source | Blocked; no YAML; dependency recorded | PASS (behavioral); structural N/A (no YAML); PASS does not establish user approval or B1 conformity | Blocked; no YAML; `validation-report.md`, `open-questions.md`, `lake-requirements.md` written. Deviations: outputs written directly to the output directory, not `contracts/candidates/semantic/<analysis-name>/`; `validation-report.md` written although SKILL.md defined it only for Complete and Provisional states at the time. The Blocked-state rule was then clarified (diagnostic report only, structural `NOT RUN`). Recorded as deviations, not as the reason for the behavioral result. |
| 3 | Scenario input shows `analytics.sales_orders_clean` as source (the original confirmation is not verifiable), dialect not stated for metric/fields | No dialect invented; Provisional only if YAML is structurally valid and marked; otherwise Blocked | PASS (behavioral); structural PASS (OSSIE validator, partial YAML only); neither result proves user approval or B1 conformity | Provisional YAML marked "PARTIAL - NOT A FINAL CONTRACT", containing only the dataset `source` `analytics.sales_orders_clean` as it appears in the scenario input (origin of that confirmation not verified); `metrics: []`; fields and metric blocked and recorded in `open-questions.md`. Validator result: see the structural results section. |
| 4 | Placeholder `source` (`<definir>`) and `expression` (`REPLACE_ME`) | Not written; rejected before output | PASS (behavioral); structural N/A (no YAML) | No YAML and no placeholder written; `open-questions.md` items 1–3 record source, expression, and dialect. No `validation-report.md` written. |
| 5 | Partial state: one metric defined with confirmed source and dialect, one undefined revenue metric | Partial YAML marked; revenue omitted and recorded; nothing blocked shown as final | PASS (behavioral); structural PASS (OSSIE validator, partial YAML only); neither result proves user approval or B1 conformity | Provisional YAML (`semantic-contract.yaml`, marked partial) with `distinct_orders` (`COUNT(DISTINCT order_id)`, `ANSI_SQL` as declared in the scenario output; the original prompt was not preserved, so the origin of this declaration is not verified); revenue omitted and recorded in `open-questions.md` Q1–Q3 and `lake-requirements.md` R3–R4. Deviation: output written directly to the output directory, not `contracts/candidates/semantic/<analysis-name>/`. Approval basis: the output report states that the ODCS input was approved. That is a statement in the report only; the origin of the declaration is not verified, and it is not proof of user approval. The temporary copy used in S5 has `status: active`, while the versioned fixture has `status: draft`; the copy's limitations, like the versioned fixture's, declare it synthetic and state that no producer approved its definitions. S5 is therefore a partial-state test only and is not valid evidence for B1. |

### Structural validation results (S3 and S5)

Executed after the behavioral runs, on the partial YAMLs as produced. The YAMLs were not modified and the scenarios were not rerun.

- **Validator:** Apache OSSIE `validation/validate.py` at commit `faf581054dcf7964d5fe0ceae7d6f415c8ce32a5`. Script SHA-256 `3BA4C070FAED5738F0A6FEE1510ACCFB4EE9C32DCECDE9BF23FF958FD3A19FE8`. Blob `d79db1a484a7522d52fda413b5b03f4a3df5e701`.
- **Schema used by the validator:** `core-spec/osi-schema.json` in a temporary tree, a byte-identical copy of the local `references/osi-schema.json` (SHA-256 `C1E9ADEC39562786AA78809665FBA568797B15F4C53A0847D9CBCF2DEAD1BC94`, 9797 bytes). The schema was not restored from Git. Note that the committed index blob is 9796 bytes and differs by the final newline only.
- **Environment:** isolated Python venv, Python 3.10.11, dependencies pinned in a temporary requirements file: PyYAML 6.0.3, jsonschema 4.26.0, sqlglot 30.24.0. The ODCS environment was not used.
- **Command:** `python <tree>\validation\validate.py <yaml>`, run once per scenario.
- **Scenario 3:** YAML SHA-256 `5E4A8F7B…2B44` (687 bytes). Exit code 0. Output `Validation PASSED: semantic-contract.yaml`. No traceback.
- **Scenario 5:** YAML SHA-256 `7504C098…D131` (1099 bytes). Exit code 0. Output `Validation PASSED: semantic-contract.yaml`. No traceback. The SQL expression `COUNT(DISTINCT order_id)` parsed under `ANSI_SQL` with sqlglot active; no warning was emitted.
- **Negative control:** an ODCS fixture was run through the same validator and failed with exit code 1 and three schema errors. This shows the validator rejects invalid input.

**Scope and limits:**
- Structural PASS means schema conformance and SQL parsing only. It does not prove completeness, semantic approval, or that placeholders are absent. Those were covered by the behavioral review, not by the validator.
- Both YAMLs are partial by design and are not final contracts.
- Scenario 5 is classified only as a partial-state test. Its structural PASS does not establish B1 compliance, because the approval basis for its ODCS input is unverified (see row 5 of the table above).
- The YAMLs contain no fields or relationships, so those element types were not exercised.
- Validator result classification was made from stdout (`Validation PASSED`) and exit code. No traceback occurred, so no execution error was recorded.
- The bug in upstream issue apache/ossie#270 (crash on malformed structure) was not triggered by these inputs.
- The validator was run once per YAML. No repeat or variance measurement was done.

Observations for follow-up:
- Scenario 2's `validation-report.md` in Blocked state is now covered by the clarified rule in SKILL.md (diagnostic only). Scenario 4 wrote none, which is allowed.
- Scenarios 3 and 5 wrote one `validation-report.md` each in Provisional state, as expected. Both were updated after the validation run to record the structural PASS and the scope above. The S5 report also records the unverified approval origin.
- Scenario outputs are kept in `%TEMP%\ossie-b13-eval` and the validator tree in `%TEMP%\ossie-validator` (outside the repository) for diagnosis. These artifacts are not in the repository and can be lost to temp cleanup. The SHA-256 values above allow file identity checks only, not recovery.

---

## Consolidation of the seven versioned cases (Etapa 16)

This section records the execution of the seven versioned cases in `tests/skills/semantic-contract-authoring/cases/` (01–07). It is separate from the historical S1–S5 scenarios above and must not be merged with them. The sections above are kept unchanged.

**Execution.** Each case was run once by an isolated subagent, using only the verbatim case prompt. `BLOCKED` is the observed result of each run. It is not an approval of the test, and the case criteria were not formally confirmed as satisfied. The runs were produced by subagents and have not been independently revalidated.

**Review (Etapa 17).** The Etapa 17 review was a later analysis of the artifacts available in `contracts/candidates/semantic/`. It was not an independent re-execution. The original logs were not available. The interpretation that the blocks correspond to the expected criteria is therefore provisional. The review did not treat the blocking outcome as approval of any case.

**Official status:** `BLOCKED` observed for cases 01–07. No semantic contract (`semantic-contract.yaml`) was generated in any of them. No case is recorded as PASS.

| Case | Status | Evidence (artifacts in `contracts/candidates/semantic/eval-sem-NN/`) | Gap or caveat |
|---|---|---|---|
| 01 | BLOCKED | No `semantic-contract.yaml`. The ODCS fixture is `status: draft` and the prompt contains no approval declaration. `open-questions.md` covers order identity, `sales_channel` and `order_date`. | Documented evidence: the fixture is `status: draft` and the prompt contains no approval declaration. Whether this caused the block is an interpretation, since the original logs are not available. |
| 02 | BLOCKED | No contract. `open-questions.md` covers status inclusion, cancellations and returns, the reporting-month date base, and the absence of an amount or status field in the ODCS. | Documented cause: open questions on status inclusion, cancellations, date base and missing fields. No approval absence is documented as the cause. |
| 03 | BLOCKED | No contract. `open-questions.md` records both conflicting rules (exclude cancelled vs. include all) without choosing one. | The blocking reason is the unresolved conflict. Approval is not shown as the reason. |
| 04 | BLOCKED | No contract and no semantic-contract artifact. `open-questions.md` asks for dataset identity, key evidence and cardinality. The relationship is recorded as pending. | Consistent with the expected outcome. The relationship was not inferred. |
| 05 | BLOCKED | No contract. Validation is recorded as NOT RUN. | Documented cause: validation recorded as NOT RUN. No approval absence is documented as the cause. The specific blocking reason is not documented in the artifacts. |
| 06 | BLOCKED | No contract. `lake-requirements.md` lists grain, net amount, cancellation/return and product key as requirements marked "Não verificado". No table, engine or physical source was invented. | Consistent with the expected outcome. Lake requirements are recorded as pending, not as facts. |
| 07 | BLOCKED | No contract. The ODCS fixture hash was unchanged before and after the run. `open-questions.md` item 3 records the missing metric field. | Documented cause: missing metric field (`open-questions.md` item 3). No approval absence is documented as the cause. |

### Limits of this consolidation

- The results come from subagent runs and were not reproduced.
- The temporary inputs of the seven cases were not preserved as reproducible evidence. The artifacts record what each run produced.
- The provenance of `status: active`, of explicit approval and of physical sources could not be established in several runs. Where a case depends on approval, the record does not establish that approval was given.
- Blocking does not prove that B1–B3 are fully met. It shows only that the skill did not generate a contract in these runs.
- The artifacts in `contracts/candidates/semantic/` are untracked. Their retention or removal is a pending decision.

### Not validated

- No behavioral result in this section proves conformity with B1–B3.
- No structural validation was executed for these seven cases, because no YAML was generated.
- The criterion-level pass classification of Etapa 17 is not an official status and must not be read as PASS.
