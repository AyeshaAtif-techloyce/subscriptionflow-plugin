# Billing review rules

Edit these to match your own collections policy. The skill applies them as written.

## Days past due

`days_overdue = today − due_date` (negative means not yet due). Use the invoice `due_date`, not `invoice_date`.

## Risk levels (first match wins)

| Risk | Rule |
|---|---|
| `critical` | `days_overdue > 60` |
| `critical` | `days_overdue > 0` and `amount_due` is in the top 10% of all open invoices' `amount_due` |
| `overdue` | `1 ≤ days_overdue ≤ 60` |
| `due_soon` | `-7 ≤ days_overdue ≤ 0` |
| `on_track` | anything else |

A customer with **2 or more** overdue invoices is "at risk": mention them by name in the summary.

## Proposed action per invoice

| Situation | Action type | Button label | Sentence to copy |
|---|---|---|---|
| Overdue or critical, not yet more than 90 days past due | `send_invoice` | Email invoice | `Email invoice <number> to the customer` |
| Critical and more than 90 days past due, or disputed | `review` | Review | `Show me the history of invoice <number>` |
| Due soon or on track | none | — | — |

## Wording

- Money: currency symbol, thousands separators, no decimals above 1,000.
- Dates: "12 Oct 2026".
- Say "Not available" for any metric the tools could not return. Never show it as zero.
