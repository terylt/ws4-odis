# Policy response

The policy engine returns a decision for the Router to validate and enforce.

## Decisions

The response has one of four outcomes:

- **Permit:** the call may proceed if the Router can enforce every condition.
- **Deny:** an applicable policy refused the call.
- **Not applicable:** no policy covered the call.
- **Indeterminate:** evaluation failed or a required source was unavailable.

These outcomes remain distinct for audit and operations: a policy refusal and an evaluation failure require different responses. Only an enforceable permit allows dispatch.

A policy that allows by default must return `permit` when no rule matches. This makes the default explicit in the policy; `not_applicable` stops the call.

The response echoes the Router's evaluation ID and includes a validity interval. Decision binding explains how the Router checks both.

## Router handling

The Router stops the call on `deny`, `not_applicable`, or `indeterminate`; on a missing, unreadable, unknown, mismatched, or expired response; or when it cannot enforce a permit in full. It records the reason so operators can distinguish policy refusals from failures.

## Obligations and guidance

A permit may carry obligations, such as obtaining manager approval before dispatch or removing a field from the result. The Router treats an unknown or unenforceable obligation as a denial. See Obligations for stages and representation.

A denial may include guidance for the caller, such as requesting manager approval before retrying a payment. The Router returns that guidance without changing or authorizing the denied call.

An engine that returns only `permit` or `deny`, without obligations or guidance, is complete. A Router without obligation support denies any permit carrying an obligation.

## Field inventory

Field names are provisional.

| Field | Required | Description |
|---|---|---|
| `evaluation_id` | Yes | Copied from the request to bind the response to one evaluation |
| `decision` | Yes | `permit`, `deny`, `not_applicable` or `indeterminate` |
| `issued_at`, `expires_at` | Yes | Decision issue and expiry times |
| `reason` | Recommended | Decision reason, distinguishing refusal from failure |
| `obligations` | Optional | Conditions attached to a permit; see Obligations |
| `advice` | Optional | Ignorable information, such as an audit note |
| `context` | Optional | Caller guidance for a later request |

## Examples

Permit for the payment in Policy request:

```json
{
  "evaluation_id": "ev_7f3c9a",
  "decision": "permit",
  "issued_at": "2026-09-30T14:02:11Z",
  "expires_at": "2026-09-30T14:02:41Z",
  "reason": { "code": "within_payment_limit" }
}
```

Denial with guidance for a USD 5,000 payment:

```json
{
  "evaluation_id": "ev_8a21d0",
  "decision": "deny",
  "issued_at": "2026-09-30T14:05:40Z",
  "expires_at": "2026-09-30T14:06:10Z",
  "reason": { "code": "over_payment_limit" },
  "context": { "message": "Payments over 1,000 USD need a finance manager's approval. Request approval and retry." }
}
```
