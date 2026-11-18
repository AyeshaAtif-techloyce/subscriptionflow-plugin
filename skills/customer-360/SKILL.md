---
name: customer-360
description: One-screen review of a single SubscriptionFlow customer — subscriptions, invoices, balance, payments, contacts, a health score and suggested next steps. Use when the user asks "tell me about Acme", "how is Acme doing", "customer 360", "account review", "prep me for a call with Acme", or names a customer and wants the full picture. Requires the SubscriptionFlow connector (staff_* tools).
---

# Customer 360

Turn "tell me about Copperfield Logistics" into a complete, interactive account card built from live SubscriptionFlow data: what they pay for, what they owe, what changed recently, how healthy the account is, and what to do next.

Read-only. Never change anything unless the user then asks for one of the suggested actions.

## 1. Find the customer

- If you have an id, use it.
- Otherwise call `staff_list_records` on `customers` with `company` `$contains` the name the user gave, and separately `name` `$contains` it (people often give either), select `id`, `name`, `company`, `email`. If both are empty, try `email` `$equals`.
- More than one match: show the matches (name, company, email) and ask which one. Do not guess.

## 2. Gather the data (run in parallel)

| What | Tool call |
|---|---|
| Customer with everything attached, including saved payment methods | `staff_get_record` on `customers`, `id`, `include` `["subscriptions","invoices","transactions","contacts","payment_methods"]` (max 5 related lists) |
| Recent activity | `staff_get_record` is limited to 5 related lists, so fetch `activities` separately only if you need it: `staff_get_record` on `customers` with `include` `["activities"]` |

Payment methods belong to the customer through `parent_id`, not `customer_id`; the related list name is `payment_methods` (underscore).

Field names you will use: customer `company`, `name`, `email`, `customer_number`, `terms`, `currency`, `auto_charge`, `portal_is_enabled`, `billing_city`, `billing_country`, `created_at`. Subscription `name`, `display_name`, `status`, `total_amount`, `next_bill_date`, `termed_start_date`, `billing_end_date`, `cancelled_at`, `cancellation_reason`, `suspended_at`. Invoice `name`, `status`, `total_amount`, `received_payment`, `due_date`, `invoice_date`. Never show a field key to the user; use plain words.

Amount due on an invoice = `total_amount − received_payment`. Days late = today − `due_date` (only for invoices that are not Paid).

## 3. Score the account

Apply `references/health.md` exactly and keep the list of reasons it produced. The score is a guide, not a fact; always show the reasons next to it.

## 4. Spot what matters

Write 2–5 short signals, most serious first. Look for:
- invoices more than 60 days late, or past due but still marked "Due";
- auto-charge on with no saved payment method;
- subscriptions cancelled or suspended in the last 30 days (possible churn), and cancellations with no reason;
- a Draft subscription whose next bill date has passed (it will never bill);
- an Active subscription whose next bill date has passed (billing may be stuck);
- no contacts and no activity on file.

## 5. Build the card

1. Read `assets/customer-360-template.txt`. It is the HTML page for the card, saved as a text file.
2. Build one JSON object matching the data contract below.
3. Replace the line `const DATA = window.__SF_DATA__ || SAMPLE;` with `const DATA = <your JSON>;` and change nothing else.
4. Show it as an interactive HTML view where the client supports it (for example an artifact or canvas), titled "<Company> – customer review".

If the client cannot render HTML, use the text fallback in section 7.

```json
{
  "generated_at": "2026-10-20",
  "customer": { "company": "", "name": "", "number": "", "email": "", "city": "", "country": "", "terms": "", "currency": "USD", "since": "2026-03-21", "auto_charge": true, "portal": true, "payment_methods": 0 },
  "health": { "score": 0, "band": "healthy | watch | at_risk", "reasons": ["-30 invoice over 60 days late"] },
  "kpis": { "open_balance": 0, "open_count": 0, "overdue": 0, "overdue_count": 0, "oldest_days": 0, "active_subs": 0, "total_subs": 0, "paid_to_date": 0 },
  "subscriptions": [{ "number": "", "name": "", "status": "", "amount": 0, "detail": "next bill 8 Nov 2026" }],
  "invoices": [{ "number": "", "status": "", "amount_due": 0, "due_date": "", "days_late": 0, "note": "" }],
  "signals": [{ "level": "bad | warn | info", "title": "", "detail": "" }],
  "actions": [{ "label": "", "say": "", "primary": true }],
  "notes": []
}
```

List open invoices first (most days late first), then up to 3 most recent paid ones. Use `null` for anything you could not get.

## 6. Suggested actions

Offer at most three, chosen from what the data shows. Each is a sentence the user can send:
- overdue invoices → "Email <company>'s overdue invoices to them";
- auto-charge on but no payment method → "Ask <company> to add a payment method in their portal";
- Draft subscription past its bill date → "Show me the Draft subscription <number>";
- recent cancellation with no reason → "Why did <company> cancel <number>?".

When the user sends one, use the normal tools; anything that changes data goes through its preview and confirmation.

## 7. Text fallback

One line with company, health score and band, open balance and overdue. Then the signals as bullets, a short table of open invoices, the subscriptions with status, and the suggested actions.
