# Audit

The Router records decisions and outcomes for calls that pass through it, including calls stopped before policy evaluation. These records support attribution across the Bridge, Router, provider adapter, and tool.

## Decision records

The Router creates one record per decision. Each includes:

- the `evaluation_id`, `request_trace_id`, receipt time, and decision time;
- the agent, running instance, and software digest;
- the principal, original delegation, and task;
- the caller-visible name, resolved tool, and affected resources;
- the overall decision and reason, plus each consulted source's decision;
- obligations and whether each was met;
- whether the call was dispatched and, if not, where it stopped; and
- a reference to any downstream credential used, never the credential itself.

The software digest supports later attribution of actions to a particular software version. A Router also records refusals made before the policy engine is consulted, such as a revoked token.

## Data minimization

The Router never records raw tokens, private keys, downstream credentials, or other secrets. Arguments and other request content may be sensitive, so it records them only when the deployment requires them; retention rules apply to all stored data.

## Record correlation

The Bridge records its grant, the Router its decision, the provider adapter the final credential exchange, and the tool may record execution. The exchange and decision records share the `evaluation_id`, linking the delegation and tool to the credential used. Each Router on a path records its own decision; the `request_trace_id` correlates those records.

## Record integrity

Deployments needing strong assurance make later alteration or deletion detectable, for example by chaining records, and store them beyond the agent host operator's control. A signed policy response may be retained as decision evidence.

## Write failures

Audit records are not policy inputs, and logging settings do not change authority. The deployment declares whether a durable record is required before dispatch. Where a durable record is required, the Router commits the decision record before sending the call and stops if that write fails; it records the dispatch outcome afterward. This proposal suggests that deployments claiming the strictest ODIS profile, which already requires tamper-evident logs, adopt this behavior. Less strict deployments may proceed and report a recording gap.
