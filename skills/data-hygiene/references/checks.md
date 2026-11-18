# Data hygiene checks

Each check is one read-only call with `limit: 5`. `total` in the response is the count. `TODAY` is today's date as YYYY-MM-DD.

| id | Severity | Title | Call | Why it matters | Fix |
|---|---|---|---|---|---|
| `stale_due` | high | Invoices past due still marked "Due" | `staff_list_records` `invoices`, `status` `$equals` `"Due"`, `due_date` `$lt` TODAY + `$sortorder` `asc`, select `id,name,customer_id,due_date,total_amount,received_payment` | Overdue reminders, dunning and overdue reports key off the status, so these invoices are never chased. | Check the job that moves invoices to Overdue; then update the status. |
| `billing_stuck` | high | Active subscriptions whose next bill date has passed | `subscriptions`, `status` `$equals` `"Active"`, `next_bill_date` `$lt` TODAY + `$sortorder` `asc`, select `id,name,display_name,customer_id,total_amount,next_bill_date` | The billing run did not invoice them, so revenue is not being billed. | Check the billing scheduler for that date and re-run billing for these subscriptions. |
| `autocharge_no_method` | high | Customers on auto-charge with no saved payment method | Two calls: `customers`, `auto_charge` `$equals` `1` (count); `payment-methods` with no filter (count). Found when customers on auto-charge > 0 and payment methods = 0; if some payment methods exist, report the customer count as "up to N" and say it is an upper bound. | Automatic charges fail for these customers. | Send each a link to add a payment method, or turn auto-charge off. |
| `draft_past_bill` | medium | Draft subscriptions past their first bill date | `subscriptions`, `status` `$equals` `"Draft"`, `next_bill_date` `$lt` TODAY + `$sortorder` `asc` | Drafts never bill, so these are forgotten sales or records to delete. | Activate the ones that were sold; delete the rest. |
| `mrr_zero` | medium | MRR report shows zero | `staff_run_report` `mrr` for the last 6 months with `currency` set. Found when every month is 0 while `staff_billing_stats` shows invoices or revenue. | MRR, churn and growth figures from this account cannot be trusted. | Check that subscriptions have term and price data the MRR report reads. |
| `cancel_no_reason` | low | Cancellations with no reason recorded | `subscriptions`, `status` `$equals` `"Cancelled"`, `cancellation_reason` `$is_null` `true` | Churn analysis by reason is impossible. | Make the cancellation reason required, and backfill recent ones. |
| `no_country` | low | Customers missing a billing country | `customers`, `billing_country` `$is_null` `true` | Tax and country reports leave them out. | Fill in the billing address. |

Add checks by adding rows. Keep each to one call, so the whole report stays fast.
