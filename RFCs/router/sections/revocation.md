# Revocation and termination

Revocation ends trust before a token expires. It may stop one decision, a delegation, a session, an agent, or activity on a principal's behalf.

## Change timing

Routine changes, such as software approval or role permissions, take effect when short-lived credentials are renewed, within one renewal period. This avoids mass reissuance after broad configuration changes.

Urgent changes, such as agent compromise or withdrawal of a principal's permission, propagate through revocation events. Components relying on affected tokens must act within the deployment's declared delay. ODIS limits that delay to 300 seconds within a trust domain.

## Revocation levels

| Level | Scope and Router action |
|---|---|
| Decision | Cancel one permit before dispatch; a retry requires a new decision. |
| Token or delegation | Reject later calls under that grant and discard derived downstream credentials. |
| Session | Reject later calls in that session. |
| Agent | Reject the agent's tokens across all tasks and sessions, including derived credentials. |
| Principal | Reject tokens naming the principal across all agents acting for them. |

Agent-level revocation is the ODIS kill switch. Session-level revocation depends on the still-open definition and identification of an agent session.

## Propagation

The Router may receive signed revocation events, fetch signed revocation lists, or query the issuer when a token is used or a cached answer expires. The OpenID Foundation's Shared Signals Framework is the preferred push mechanism. Queries add issuer availability to the call path; events and lists permit local checks.

The Router accepts revocation data only from a party authorized to revoke the subject, with integrity and replay protection and an unambiguous subject identifier. Existing event standards cover people and their sessions but lack standard identifiers for agents, delegations, and agent sessions. ODIS must define those subjects for revocation events.

## Enforcement points

- **Token verification:** Reject a revoked caller before policy evaluation.
- **Before dispatch:** Check before obtaining a downstream credential and again as the final step before sending the call. Approval or other work may have delayed dispatch after the first check.
- **Before result release:** Check again and withhold the result if authority was revoked. For a stream, stop further release. The tool action cannot be undone, and data already released cannot be recalled.
- **After the call:** No further revocation check is needed, but after-call obligations still run for actions already taken.

The Router discards cached downstream credentials derived from revoked authority. With events or lists, repeated checks are local lookups; issuer queries incur another remote call.

## Unavailable status

If current revocation data is unavailable, the Router may rely on cached data only within the declared delay. Once it is older, the Router stops accepting affected tokens until the data is refreshed. Deployments requiring continued calls must make the revocation source as available as the Router.
