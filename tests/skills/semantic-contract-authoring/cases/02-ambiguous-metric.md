# Case 02 — Ambiguous metric

## Objective

Verify that the skill asks questions when a metric's business meaning is under-specified.

## Prompt

“Create a revenue metric and show it by month.” The ODCS contract contains order amount, order date, and order status, but no approved definition of revenue.

## Expected behavior

- Does not assume revenue means sum of order amount.
- Asks targeted questions about material uncertainties, such as status inclusion, cancellations/returns, and which date defines the reporting month.
- May identify candidate interpretations but does not silently select one.
- Does not emit a definitive metric expression until the key definition is confirmed.
