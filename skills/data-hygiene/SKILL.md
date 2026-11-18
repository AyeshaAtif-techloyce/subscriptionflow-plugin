---
name: data-hygiene
description: Health check of a SubscriptionFlow account's billing data — records stuck in the wrong state, missing details, and reports that cannot be trusted — with counts, examples and how to fix each. Use when the user asks to "check my data", "is anything broken in billing", "data health", "audit my account", "clean up", or wants to know why numbers look wrong. Requires the SubscriptionFlow connector (staff_* tools). Read-only.
---

# Data hygiene check

Run every check in `references/checks.md` against live data, then show one interactive report: each problem with how many records it affects, a few examples, why it matters and how to fix it.

This skill only reads. Fixes are suggestions the user can ask for afterwards; anything that changes data then goes through the normal tools and their preview and confirmation.

## 1. Run the checks

- Use today's date (YYYY-MM-DD) wherever a check says `TODAY`.
- Run the checks in parallel. Each is one `staff_list_records` call (or the report named) with `limit: 5`: the response's `total` is the count, and its `records` are the examples. Do not page through everything.
- If a call fails, mark that check `unavailable` with the reason; never report it as clear.
- A check with `total: 0` is `clear`.
- Never show a field key to the user; use plain words.

## 2. Make examples readable

Examples carry customer ids. Look up names once for all of them: `staff_list_records` on `customers`, `id` `$in` [the ids], select `id`, `name`, `company`. Show "Company (Name)" where a company exists.

## 3. Build the report

1. Read `assets/data-hygiene.html`.
2. Build one JSON object matching the contract below, checks ordered high → medium → low, then clear, then unavailable.
3. Replace the line `const DATA = window.__SF_DATA__ || SAMPLE;` with `const DATA = <your JSON>;` and change nothing else.
4. Show it as an interactive HTML view where the client supports it (for example an artifact or canvas), titled "Data health – <today's date>".

```json
{
  "generated_at": "2026-10-20",
  "account": "Account or tenant name",
  "checks": [{
    "id": "stale_due",
    "title": "Invoices past due still marked \"Due\"",
    "severity": "high | medium | low",
    "status": "found | clear | unavailable",
    "count": 43,
    "detail": "One or two sentences on what was found, with the most useful number.",
    "why": "Why it matters, in one sentence.",
    "fix": "How to fix it, in one sentence.",
    "examples": [{ "label": "PERF-IN-000087", "sub": "Customer · due 12 Feb 2025 · $17,643.23" }],
    "ask": "List the 43 invoices that are past due but still marked Due"
  }],
  "notes": []
}
```

## 4. Text fallback

A table: check, count, severity, one-line fix. Then the top three examples for each high-severity finding.
