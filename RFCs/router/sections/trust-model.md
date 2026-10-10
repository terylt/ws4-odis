# Trust model

This section describes the Router's trust assumptions and input sources. It uses the layers and terms defined in Scope.

## Router isolation

The Router must be outside the agent's control. Content an agent reads may contain instructions that redirect its behavior (prompt injection), so anything the model can influence cannot be trusted to enforce policy: tools that run code or write files, plugins loaded into the agent's process, and any memory or file those can reach.

The agent must not be able to read Router keys, alter its configuration or state, or bypass it on the way to a tool. The Router may run beside the agent, at a gateway, or in front of a tool if those protections hold.

## Router and policy engine

The Router verifies tokens, resolves the actual tool and resource, and enforces the decision and any conditions. A policy engine applies organizational rules to Router-supplied inputs. They may run together or separately; the Router identifies any unverified inputs it passes to the policy engine.

## Trust sources

The Passport verifies raw evidence about the agent, such as a software-image signature or platform statement, before issuing a credential. The Router receives the Bridge's signed account of the Passport's conclusions, not the raw evidence.

The Bridge records authority granted by an entitled party, such as a principal or administrator. Principal, task, and permissions are grant decisions rather than facts established by technical evidence. The Router relies on the Bridge's signed claims about them.

The agent chooses the requested tool, operation, and arguments. The Router treats these as inputs to evaluate, retains the request, and forwards only the request that was evaluated. Other inputs, such as a self-reported model, a tool's read-only description, or a risk signal with an unconfirmed source, may be useful but must be marked unverified for the policy engine.

## Input provenance

| Input | Source and treatment |
|---|---|
| Requested tool, operation, and arguments | Agent-supplied; evaluated as untrusted input. Only the evaluated request is forwarded. |
| Agent and running instance | Passport conclusions carried in the Bridge token. |
| Principal and task | Authority recorded in the Bridge token. |
| Authority for the call | Bridge token, already narrowed by all applicable limits; the Router does not recompute it. |
| Session | Bridge token or Router records, never an agent-supplied identifier. |
| Actual server and tool | Router configuration or a trusted registry; the Router resolves aliases chosen by the agent. |
| Time and decision identifier | Assigned by the Router for freshness and replay checks. |
| Risk signals | Source, freshness, and subject checked by the Router; otherwise marked unverified. |

## Layered trust

The Bridge verifies the Passport credential, copies its conclusions into a Bridge token, and signs that token. The Router verifies the Bridge token rather than parsing the Passport credential. The token is bound to the same agent-held private key as the Passport credential, preventing use of a copied token by another holder.

Each layer therefore depends on the adjacent layer's interface. A Router need only support Bridge tokens, though its trust depends on the Bridges it accepts. A careless Passport, over-permissive Bridge, or compromised policy engine can still cause incorrect decisions that the Router cannot detect.

## Threats addressed

This model addresses:

- Forged agent identity or authority.
- Access through an alias that policy does not cover.
- New or split sessions used to evade limits or detection.
- Replay of stale tokens or decisions.
- Forwarding a request different from the one evaluated.
- Direct tool access that bypasses the Router.
- A host operator disabling a Router on the agent's machine. Deployments that must withstand host compromise need an independently controlled Router on every path to the tool.
