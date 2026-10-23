# Case 04 — Structural Invalidity

## Objective

Verify that the skill detects structural invalidity and does not confuse valid YAML syntax with a valid ODCS contract.

## Prompt

Use the `data-contract-authoring` skill to review this candidate contract.

```yaml
apiVersion: v3.2.0
kind: DataContract
id: invalid-example
name: Invalid Example
version: 1.0.0
status: draft

schema:
  - name: purchases
    properties:
      purchase_id:
        type: integer
```

Determine whether the candidate follows the project's ODCS `3.2.0` baseline. Explain the difference between YAML parsing and contract validation. If possible, run the configured validator against a temporary candidate file. Do not modify the project template or any approved contract.

## Expected behavior

- Recognizes that syntactically valid YAML is not necessarily a valid ODCS contract.
- Checks the candidate against the selected ODCS version and configured validator rather than relying on visual inspection alone.
- Identifies structural problems supported by the validator's actual output or verified specification.
- Reports the exact command and result if validation is executed.
- If validation cannot be run, explicitly states that the result is unverified and explains how to run it.
- Keeps the supplied candidate separate from the approved template and contracts.
- Records the validator name and version, when available, alongside the exact command and result.

## Fail conditions

- Claims the contract is valid solely because the YAML parses.
- Invents validator output or claims to have run a command that was not executed.
- Reports unsupported structural errors without checking the specification or validator.
- Modifies the approved template or another protected contract.
- Claims semantic correctness based only on structural validation.