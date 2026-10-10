# Availability and dependencies

The Router needs trusted keys, revocation data, routing information, policy decisions, and state to handle a call. Deployments can reduce live dependencies by supplying inputs before they are needed, but must bound how stale those inputs may become.

## Input delivery

- **With the request:** The signed Bridge token carries agent, principal, and authority facts. With trusted Bridge keys held locally, the Router need not contact the Passport, Bridge, or registry for each call.
- **Ahead of time:** Signed bundles can provide routing configuration, target facts, and Bridge keys. Revocation events and risk signals can be pushed into a local store. The Router then uses local lookups.
- **At the Router:** Rate-limit counters, retained calls under evaluation, and pending audit records require Router-controlled state.

Preloaded data reduces call-time dependencies but may become stale. Each source needs a declared freshness bound, such as token lifetime, configuration expiry, or the allowed revocation delay.

## Dependency behavior

| Dependency | Delivery | If unavailable |
|---|---|---|
| Trusted Bridge keys | Locally held and refreshed | Reject tokens whose signing keys cannot be confirmed. |
| Revocation data | Pushed events or fetched lists | Stop affected calls once cached data exceeds the declared delay. |
| Routing and target facts | Signed bundles | Use the last valid configuration until it expires; deny calls that cannot be resolved. |
| Policy engine | Local or separate service | Stop calls; no decision can be made. |
| Rate-limit counters | State shared by Router instances | Stop calls subject to authority limits. |
| Downstream credential issuer | Identity provider reached by the adapter | Stop calls needing a new credential; reuse a cached one only within its limits and after a separate permit. |
| Audit store | Local buffer or central store | Follow the deployment's declared rule: stop calls or report a recording gap. |

## Failure handling

A call cannot proceed when a required check is incomplete. Deployments needing continued service must make the relevant dependency as available as the Router, for example by running the policy engine locally, pushing revocation data, or delivering configuration ahead of time. An in-process policy engine also removes the need to authenticate and protect messages over a Router-to-engine network connection. See Decision binding.
