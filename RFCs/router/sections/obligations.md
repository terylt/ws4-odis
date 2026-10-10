# Obligations

An *obligation* is a condition attached to a policy permit. Each obligation specifies when it must be fulfilled.

## Stages

The stage determines whether the Router may dispatch the call, release its result, or commit to follow-up work:

- **Before the call:** The Router holds the call until the condition is met. For approval, it may wait or return a pending status for a later retry. Approval applies only to the exact call evaluated; it cannot authorize a larger payment or another invoice.
- **Before result release:** The call proceeds, but the Router withholds the result until the condition is met, such as removing a bank account field. If fulfillment fails, the result is not released. Conditions that must prevent the action belong before the call, since dispatch cannot be undone.
- **After the call:** The Router performs follow-up work, such as notification or retention. Before dispatch, it must secure a durable commitment to that work, for example in a queue that survives a restart. If it cannot, the call is denied.

All obligations on a permit must be fulfilled. An unknown or unenforceable obligation makes the permit a denial, as defined in Policy response.

## Definition and support

Each obligation identifies its definition by URL and version, its stage, and any parameters. Deployments publish supported definitions, versions, and the component responsible for each. A bare name such as `redact` is insufficient because its meaning may differ between deployments. ODIS does not currently define a standard list.

A Router may support no obligations. It then denies any permit carrying one. An engine can instead deny with guidance about a later request, as described in Policy response.

## Advice

The separate `advice` field carries nonbinding information, such as an audit note or operator warning. The Router may act on it or ignore it; failure to process advice does not stop the call.

## Example

A permit for the USD 250 payment, requiring removal of the bank account field and notification to finance:

```json
{
  "evaluation_id": "ev_7f3c9a",
  "decision": "permit",
  "issued_at": "2026-09-30T14:02:11Z",
  "expires_at": "2026-09-30T14:02:41Z",
  "obligations": [
    {
      "id": "https://example.com/obligations/remove-fields",
      "version": "1",
      "stage": "before_release",
      "params": { "fields": ["bank_account"] }
    },
    {
      "id": "https://example.com/obligations/notify",
      "version": "1",
      "stage": "after_call",
      "params": { "channel": "finance-payments" }
    }
  ]
}
```

## Open question: common types

Should ODIS define common obligation types with fixed handling for approval, field removal, and notification, while leaving their details to deployments? This could make policies portable without standardizing a full vocabulary.
