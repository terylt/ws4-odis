# Placement and composition

A Router may run beside an agent, in a gateway, or in front of a tool. A deployment may use several Routers on one path, and each Router may consult several decision sources.

## Placement criteria

Placement depends on isolation, visibility, and policy coverage:

- **Isolation:** Nothing the model controls may reach the Router's code, configuration, keys, or the downstream credentials it uses, for example through a tool that runs code or writes files, or a plugin loaded into the same process. A separate protected component beside the agent can satisfy this. A Router inside the agent's process often cannot, but an embedded check can be sound if the agent has no such tools and keys and credentials are held outside the process. See Trust model.
- **Visibility:** A Router can enforce only rules for which it sees the needed facts.
- **Coverage:** Organization-wide rules belong on paths used by all agents, including unregistered agents and those owned by other teams. Agent-specific rules may run near that agent.

| Position | Can observe | May lack |
|---|---|---|
| Beside the agent | Each call, its sequence within a task, and the agent's declared purpose (unverified) | Other agents' activity and proof that no alternate tool path exists |
| Gateway | Calls to tools behind it and totals across those agents | The agent's internal reason for a call |
| In front of the tool | Effects on the tool's data, result size, and local access rules | Delegation context unless forwarded |

A gateway is a useful starting point for organization-wide policy because it is outside the agent's control and sees traffic to the tools behind it. Agent-specific and tool-specific rules may require additional Routers.

## Multiple Routers

A call proceeds only if every Router on its path permits it. Each verifies the call and applies its own policies; another Router's permit cannot replace its decision. Each records its own outcome, including a denial that prevents the call from reaching later Routers.

The deployment must identify which policies each Router enforces and ensure each required policy is enforced on every path it covers. Merely placing a Router somewhere on the path does not establish policy coverage.

## Host compromise

If host operators may disable controls on the agent's machine, every protected path to a tool must cross a Router they cannot modify. That independently administered Router enforces all rules that must survive host compromise, including authority limits, revocation, and limits on destructive operations.

Local file and shell tools may have no network path through such a Router. A deployment requiring this protection must route those calls through an independent Router or explicitly exclude them from its coverage claim. A local Router may still enforce additional agent-specific rules.

## Decision sources

A Router may consult a policy engine, rate-limit service, fraud check, or other sources. Any source may stop a call or add conditions; none may override another's refusal or alter the verified inputs presented to the others. The Router records each source's decision.

Order matters when a source counts usage. As described in Limits and state, authority limits count only calls permitted by the other sources and dispatched to the tool.
