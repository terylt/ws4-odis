# Glossary

**Advice.** Nonbinding information returned by a policy engine. The Router may ignore it without stopping a call.

**Agent.** Software that uses an AI model to call tools and services on a principal's behalf.

**Authority limit.** A cap on delegated activity, such as payments per hour, counted against verified identifiers or resolved targets. Compare load protection.

**Binding.** Protocol-specific rules that map calls to policy actions and resources and identify arguments relevant to a decision.

**Bridge.** The ODIS layer that records who an agent is working for and what it may do, and issues a token carrying that authority.

**Claim.** A statement in a token, such as an agent identity or permission.

**Decision identifier.** The Router's unique `evaluation_id` for one policy evaluation, echoed in its response.

**Decision source.** A policy engine, rate-limit service, or other check consulted by the Router. Any source may stop a call or add conditions.

**Delegation.** A grant of authority from a principal to an agent, or from one agent to another. The original delegation is the first grant in a chain, from the principal.

**Dispatch.** Sending the evaluated call to the tool. Nothing reaches the tool before dispatch.

**Downstream credential.** A credential accepted by a tool, obtained or applied after a permit and never given to the agent.

**Gateway.** A component that offers tools from several servers under its own names. The Router often runs within it.

**Indeterminate.** A decision outcome meaning the policy engine could not reach a decision. The call stops.

**Kill switch.** Agent-level revocation that invalidates the agent's tokens across tasks and sessions and discards derived credentials.

**Load protection.** An early limit that counts every request against observable attributes such as network address or connection. Compare authority limit.

**Namespace.** A named group of tools within a gateway, such as a virtual server, that may be offered to different agents.

**Native-mode target.** A tool that understands ODIS tokens and checks them itself.

**Not applicable.** A decision outcome meaning no policy covered the call. The call stops.

**Obligation.** A condition attached to a permit, with a stage at which it must be met, such as approval before dispatch.

**Passport.** The ODIS layer that checks what software an agent is running and where, and gives it a credential.

**Permit.** The only decision outcome that may allow dispatch, if the Router can meet all obligations.

**Policy engine.** The component that applies an organization's rules to a call and returns a decision.

**Policy input declaration.** A published list, per engine, of optional fields and argument paths it receives, their sources, and required verification levels.

**Principal.** The person or service an agent is working for.

**Proof of possession.** Evidence that the caller controls the private key tied to a token; a copied token alone is insufficient.

**Provider adapter.** The Bridge component that obtains a tool credential from Bridge-recorded authority.

**Registration record.** An organization's record of an agent: its owner, the software it may run, and the rules that apply to it.

**Resolved target.** The actual server, operation, and resources determined by the Router, including the tool when applicable.

**Revocation.** Ending trust in a token, delegation, session, agent or principal before its lifetime runs out.

**Router.** The ODIS layer that verifies calls, resolves targets, obtains policy decisions, and enforces them on the path to tools.

**Token.** A signed set of claims issued by one party and checked by another.

**Trace identifier.** An identifier that follows a request across every component that handles it, used to join their logs. It does not distinguish separate decisions about the same request.

**Trust domain.** Systems under one administrative authority that trust the same issuers, often but not always one organization.

**Verify.** Check a token's trusted issuer, expiry, revocation status, and proof that the presenter holds its bound key.
