# Request flow

An invoice payment illustrates how the Router handles one call. Later sections define each step in detail.

## Scenario

Alice, who works in finance, authorizes an agent to reconcile quarterly invoices and pay invoices up to USD 1,000. The Bridge issues a token naming Alice, the task, and the limit, and binds it to the agent's private key.

The agent requests `invoices.pay` through a gateway tool named `finance__erp`, with `invoice_id` 4411, `amount` 250, and `currency` USD. The gateway name is an alias for a payment tool on the company's ERP server; the name alone does not identify the actual target.

## Processing

```
  Agent
    │  call, with the Bridge's token
    ▼
  ╔═ Router ════════════════════════════════════════════════════════╗

    1 Intake             keeps an unchanged copy of the call
    │
    ▼
    2 Token verifier     ◀── keys of trusted Bridges, revocation information
    │
    ▼
    3 Target resolver    ◀── routing configuration, tool registry
    │
    ▼
    4 Request builder    ──── decision request ────▶  Policy engine
                                                          │
    6 Enforcer           ◀─── decision (5) ───────────────┘
    │  works from the copy kept at step 1
    │
    ├──▶ 7 Audit record
    │
  ╚═│═══════════════════════════════════════════════════════════════╝
    ▼
  ERP payment tool    receives the evaluated call, or nothing
```

1. **Receive.** The Router retains the call as received, records the time, and assigns a decision identifier. Later steps refer to the retained request.

2. **Verify.** The Router checks the Bridge token's trusted issuer, expiry, revocation status, and proof of possession. It obtains the agent identity, Alice, the task, and the USD 1,000 limit from the token. See Router as verifier.

3. **Resolve.** Trusted routing configuration maps `finance__erp` to the ERP payment tool and interprets `invoice_id` 4411 as the affected invoice. A trusted registry may add target attributes such as owner and data sensitivity. The agent supplies the name and arguments; the Router determines their meaning.

4. **Request a decision.** The Router supplies the policy engine with the agent, principal, task, authority, resolved target and invoice, amount, and currency. It sends only the inputs the policy requires, never the agent's tokens or keys. See Policy request.

5. **Validate the decision.** Under the example policy, USD 250 is within Alice's limit, so the engine returns a permit. The Router checks that the response matches this decision and remains fresh.

6. **Enforce.** Any decision the Router cannot fully enforce prevents dispatch. A condition such as manager approval holds the call until satisfied. Immediately before dispatch, the Router checks revocation again, because authority may have been withdrawn while the call was held. Because the ERP server does not accept Bridge tokens, the Bridge's ERP adapter then obtains a credential limited to this payment. The Router attaches it, without exposing it to the agent or changing the evaluated operation and arguments, and sends the retained call to the ERP tool. See Downstream credentials.

7. **Record.** The Router writes an audit record linking the agent, Alice, the task, the decision, and the dispatched call.

The Router components may share a process or run separately, but the agent must not control them. Verification and target resolution use trusted keys, revocation data, routing configuration, and registry records rather than agent-supplied claims.

## Variations

- A USD 5,000 payment exceeds the limit and is denied before reaching the ERP server.
- If withdrawal of Alice's authority has revoked the token, verification stops the call before policy evaluation.
- Another alias for the same payment tool resolves to the same target and policy.
- For a web API, resolution uses the request method, address, and relevant fields; the remaining steps are the same.
