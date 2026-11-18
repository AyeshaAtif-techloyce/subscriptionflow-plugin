# Customer health score

Start at **100** and subtract. Never go below 0. Record each deduction as a reason, e.g. "-30 invoice over 60 days late".

| Deduction | When |
|---|---|
| −30 | any unpaid invoice more than 60 days past its due date |
| −15 | otherwise, any unpaid invoice 1–60 days past its due date |
| −15 | `auto_charge` is on and the customer has no saved payment method |
| −10 each, at most −20 | a subscription cancelled, suspended or expired in the last 30 days |
| −10 | a Draft subscription whose next bill date has passed |
| −10 | no Active subscription at all |
| −5 | no contacts on file |

## Bands

| Score | Band | Pill text |
|---|---|---|
| 75–100 | `healthy` | Healthy |
| 50–74 | `watch` | Watch |
| 0–49 | `at_risk` | At risk |

Edit the numbers to match your own customer-success policy. The skill applies them as written.
