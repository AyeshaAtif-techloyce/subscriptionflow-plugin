# SubscriptionFlow plugin for Claude

Connect Claude to your [SubscriptionFlow](https://www.subscriptionflow.com/) billing account and add four skills for the jobs finance and customer-success teams do most: a billing review, a one-screen customer summary, a data health check and guided account setup. The plugin installs the SubscriptionFlow connector (`https://mcp.subscriptionflow.com/mcp`) and the skills together, so you can ask about customers, subscriptions, invoices and revenue in plain language, and make routine billing updates without leaving the chat.

Skills add no new permissions. Everything goes through the connector, which signs you in with your own SubscriptionFlow email and password, limits every action to your own SubscriptionFlow permissions, and shows a preview of every change that is applied only after you confirm it. The plugin does not charge, refund or retry payments.

## Install (Claude Code)

Add this repository as a marketplace, then install the plugin:

```
/plugin marketplace add AyeshaAtif-techloyce/subscriptionflow-plugin
/plugin install subscriptionflow@subscriptionflow
```

On first use Claude asks you to connect to SubscriptionFlow: sign in, choose the account if your email belongs to more than one, and approve.

You need a SubscriptionFlow account. Sign up or log in at [subscriptionflow.com](https://www.subscriptionflow.com/).

## Skills

| Skill | You say | You get |
|---|---|---|
| billing-review | "billing review", "overdue invoices" | An interactive dashboard: outstanding and overdue amounts, ageing, revenue trend, failed payments, renewals and a next step for each invoice |
| customer-360 | "tell me about Copperfield", "prep me for a call with ..." | An account card: subscriptions, invoices, balance, payment methods, a health score with reasons, what stands out and suggested next steps |
| data-hygiene | "check my data", "is anything broken in billing" | A report of data problems (stale statuses, stuck billing, auto-charge with no card, drafts past due, missing reasons) with counts, examples and fixes |
| account-setup | "help me set up", "what's left to set up" | Reads your setup checklist, does the next step with you (creating a product, plan or customer with a preview and confirmation) and points you to SubscriptionFlow for what the connector cannot do |

Rules such as risk levels and health-score thresholds live in each skill's `references/` folder, so you can change them without touching code.

## What the connector can do

Look up and search records, run the account's business reports (MRR, churn, invoice ageing, revenue by product), export records to CSV, and search the SubscriptionFlow help articles. With a preview and confirmation each time, it can create or update customers, contacts, leads, deals, products, plans and coupons, create quotes and one-off invoices, email an invoice to a customer, void an unpaid invoice and archive a customer. Ask "What can I do with this connector?" to see which tools your role allows.

## ChatGPT

The same skills are packaged for ChatGPT. `plugin.json`, `mcp.json` and `assets/` hold the ChatGPT manifest and the connector settings (OAuth with dynamic client registration).

## Support and privacy

- Support: https://www.subscriptionflow.com/contact/
- Privacy policy: https://www.subscriptionflow.com/privacypolicy/
- Terms of service: https://www.subscriptionflow.com/terms-and-conditions/

## License

[MIT](LICENSE)
