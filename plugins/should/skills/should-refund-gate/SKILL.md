---
name: "should-refund-gate"
description: "Decide support refunds and exceptions with the should MCP: policy checks in code, amount-scaled thresholds, distress and fraud checks, queue routing and chargeback outcomes."
---

# Refund decisions with should

Use when a support agent or automation must approve, deny or route a refund, credit, return or billing exception.

## 1. Gather the facts

Build the context from the helpdesk and billing system, not from the customer's claims alone:

```json
{"request": {"message": "<customer's words>", "amount_usd": 0, "reason": "..."},
 "order": {"order_id": "...", "purchased_on": "YYYY-MM-DD", "days_since_purchase": 0, "amount_usd": 0, "status": "delivered"},
 "customer": {"tenure_months": 0, "refunds_last_12_months": 0, "chargebacks_ever": 0},
 "policy": "<the refund policy text>"}
```

Compute `days_since_purchase` and amounts yourself.

## 2. Hard rules in code

- Outside the refund window, over the refund cap, or refund larger than the order: not automatic. Route to a person with the reason.
- A prior chargeback, or a payment-method mismatch: always a person.

## 3. Judge with one batch

`should_batch` with:
- `policy_fit`: "Is this refund request within the refund policy?"
- `not_fraud`: "Is this request free of signs of refund abuse or fraud?"
- `calm`: "Is the customer not in distress or at risk of escalation if handled automatically?"

Scale the yes bar with the amount: under $50 use 0.85, $50 to $250 use 0.92, over $250 always a person. Use `thresholds: {"yes": <bar>, "no": 0.8}` on each question.

Approve automatically only when `blocked_by` is empty. Never deny automatically when `calm` did not answer yes: a distressed customer gets a person.

## 4. Route what is not automatic

Ask one choice question with `should`: "Which team should handle this exception?" with described options, for example billing (charges, duplicate payments), retention (loyal customer outside policy), logistics (damaged or missing delivery), fraud review. Below the confidence bar, send it to the general queue. Attach the blocking question names, their `lean`, and the `decision_id`s.

## 5. Tell the customer

Approved: confirm the amount and timing. Routed: say a person will review it and when to expect an answer. Never tell the customer the model's scores.

## 6. Close the loop

After 60 days, record outcomes with `record_outcome`: a chargeback or reversal on an approved refund is `incorrect`; a routed case a person approved means the automatic answer was a missed yes. Use the error rate by amount band to raise or lower the bars.