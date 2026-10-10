# Limits and state

Rate limits depend on activity across calls. A limit must define what is counted, over what period, and which key shares the count.

## Limit definitions

ODIS requires limits on agents, especially for destructive operations. A limit may count all calls, a class of operations such as payments, or a quantity such as the total amount paid. It also defines a window, such as a minute, hour, or day. Ten payments per hour and USD 5,000 per hour constrain different risks.

Destructive operations include actions such as deleting, paying, or sending when the effect is hard to undo. Classification must come from trusted target information, such as a registry, rather than a tool's self-description. An incorrect read-only label must not evade a destructive-operation limit.

## Limit purposes

**Load protection** guards against floods and runaway loops. It may run before token verification, where it can protect verification and policy-engine capacity. It counts every request, including denials, and uses only information available at that point, such as network address, connection, or client certificate. Using an unverified claimed identity would let forged traffic exhaust another agent's allowance. The deployment chooses where to place this control.

**Authority limits** cap the use of delegated authority and are required by ODIS. They use verified identities and resolved targets. The Router checks them before dispatch but counts only permitted calls that were dispatched. Counting refusals would let denied traffic exhaust an allowance. If a rate-limit service checks and counts in one operation, the Router should consult it after the policy decision or account for that behavior when setting the limit.

## Count keys

An authority limit is keyed by one or more of the following:

| Key | Scope and constraint |
|---|---|
| Agent | One agent; sub-agents receive separate allowances. |
| Original delegation | All work under a grant, including sub-agents; the Bridge token carries this identifier so fan-out cannot multiply the limit. |
| Principal | Work done for one person or service across agents. |
| Task | One task; sub-agents may have different task IDs, so the original delegation is more reliable for shared work. |
| Session | One run of activity, once ODIS defines sessions. |
| Tool or operation | The resolved target, shared by all aliases for it. |

A deployment may combine keys, such as payments per delegation or total amount per principal. A call must satisfy every applicable limit. Each authority-limit key comes from a verified token, resolved target, or Router record, never an agent-chosen identifier. Aliases for the same tool share a count.

## Enforcement

Most policy engines do not retain counters. The Router may enforce configured limits, pass current totals to the policy engine, or consult a dedicated rate-limit service such as Limitador. Each decision source may stop a call; none may override another's refusal. See Placement and composition.

## Counter state

Router instances enforcing the same limit must share counters. Counts must survive Router restarts; otherwise traffic spread across instances or restarts can exceed the limit. If counter state is unavailable, calls subject to the limit stop until it can be checked. Deployments requiring continued calls must make the store as available as the Router.
