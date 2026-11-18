---
name: account-setup
description: Guide a new SubscriptionFlow account through setup, one step at a time — business details, payment gateway, first product and plan, first customer, first subscription, invoice template, taxes and team. Use when the user says "help me set up", "get started", "onboarding", "what's left to set up", "set up my account", or is clearly new to SubscriptionFlow. Requires the SubscriptionFlow connector (staff_account_setup and the create tools).
---

# Account setup

Get a new account to the point where it can bill its first customer, without overwhelming the user: check where they are, do the next step with them, check again.

## 1. Check where they are

Call `staff_account_setup`. It returns every step as `done` / `todo` / `unavailable`, overall progress and `next` (the first required step not done). Where the client shows the setup checklist card, let it show the list; otherwise summarise in one line ("4 of 5 required steps done — next: add your business details") and list what is left.

If `complete` is true, congratulate them briefly and offer the next useful things: a first billing review, inviting the team, or turning on taxes.

## 2. Do ONE step at a time

Work on `next` unless the user picks another. Never run several steps in one go.

| Step | What you can do in the chat | What happens in SubscriptionFlow |
|---|---|---|
| `business_profile` | Explain what is missing (the `missing` list). | Settings > Business Profile. You cannot change settings through the connector; say so. |
| `payment_gateway` | Explain the options and that credentials are entered only in SubscriptionFlow. | Settings > Payment Gateways. **Never ask for or accept API keys, secrets or card details in the chat.** |
| `product_with_price` | Create the product and its plan with `staff_create_record` (`module: "products"`, then `module: "plans"` linked to it). Ask for the names first. | Prices (charges) are added on the plan in SubscriptionFlow (Products > the plan > add a charge). The connector cannot set prices; say so plainly and point them there. |
| `first_customer` | Create the customer with `staff_create_record` (`module: "customers"`). Ask for name and email; nothing else is required to start. | — |
| `first_subscription` | The connector does not create subscriptions. Say that a priced plan and a customer now exist and point them to SubscriptionFlow (Subscriptions > New) to start one. | Subscriptions > New. |
| `invoice_template`, `taxes`, `team` | Optional. Explain what each does and where it lives (the step's `how`). | Settings. |

Field names: call `staff_describe_module` for `products`, `plans` or `customers` before creating, and use only fields it lists. Never guess required fields.

## 3. Every change is previewed

`staff_create_record` is two-step: call without `confirm_token`, show the preview (or let the confirm card show it), and apply only after the user says yes. One change per confirmation.

## 4. Check again

After a step is done, call `staff_account_setup` again and show the new progress, so the user sees it move. Then offer the next step.

## Tone

Short and encouraging. Use their words, not field keys. If the user is not an administrator and a step needs settings they cannot reach, call `staff_whoami` and tell them which permission to ask for.
