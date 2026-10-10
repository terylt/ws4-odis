# Changes to ODIS

This section maps the Router proposal to the current ODIS draft. Section numbers, requirement IDs, and issue numbers refer to that draft and its issue tracker.

## Proposed changes

| ODIS area | Current draft | Proposal | Router section | Issue |
|---|---|---|---|---|
| §6.4 policy input | Sends complete registration, runtime credential, and delegation records with each action | Send Router-checked facts and labeled unverified inputs grouped by agent, principal, authority, scope, target, and environment. Include optional inputs only when declared; never send raw credentials or attestation evidence. Add schema version and evaluation ID. | Policy request | #34 |
| §6.4 policy output | Permit or deny, reason, and obligations | Define permit, deny, not applicable, and indeterminate. Dispatch only on an enforceable permit; distinguish refusals from failures and define a response validity interval. | Policy response | #24 |
| §6.4 obligations | Untyped list without processing rules | Require a versioned definition and stage: before dispatch, before result release, or after the call. Deny on unknown obligations; separate advice; allow implementations without obligation support. | Obligations | Split from #24 |
| Decision binding | Unspecified | Use a unique evaluation ID, retain the evaluated call, and act on a fresh decision once. Authenticate the Router and any external policy engine. | Decision binding | Split from #24 |
| §6.3, L2-13, L3-02 verification | Issuance checks defined; call-time checks only partly defined | Verify a trusted Bridge token carrying Passport conclusions and registration facts. A receiving organization's Bridge checks incoming cross-organization credentials and issues its own token. Recheck mutable facts on each call. | Router as verifier | #27, #28, #33 |
| §3.3 Router role | Discovery, governance, and safeguards; no routing rule | Resolve and apply policy to the actual server, operation, and resources. | Target resolution | — |
| §7.2 gateway pattern | Exchanges downstream credentials before governance checks | Decide first, then obtain or apply a credential narrowed to the decision where possible and always withheld from the agent. Reuse only after a separate permit for each call; link exchange and decision records. | Downstream credentials | — |
| L3-03 limits | Configurable per-agent limits, especially for destructive operations | Define count units and windows, trusted destructive classification, verified count keys that survive delegation fan-out, separate load limits, and shared counters. | Limits and state | — |
| L3-04, L3-05 revocation | Terminates affected sessions; kill switch cascades to active sessions | Define five revocation levels and checks before dispatch and result release. Define event subject IDs for agents, delegations, and sessions. | Revocation and termination | #25 |
| L3-02, L3-08 checkpoints | Free placement; gateway recommended; composition undefined | State placement criteria. Require every Router on a path to permit, and each decision source to respect others' refusals. Protect paths from host compromise with an independently administered Router. | Placement and composition | #23, #31 |

## Open questions

Unresolved questions, including results, sessions and standard obligation types, are collected in Open questions.

## Unaffected areas

Passport issuance and Bridge authority grants remain defined by their own layers, except for the facts the Router must receive. This proposal does not select a policy language, policy engine, or Router-to-engine transport.
