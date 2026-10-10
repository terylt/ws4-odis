# Summary

ODIS divides agent access across three layers. The Passport verifies the running agent, the Bridge records the principal and granted authority, and the Router governs calls to tools and services. ODIS defines the first two layers in more detail; this document specifies how the Router evaluates and enforces each call.

## Design choices

- **Isolation and roles:** The agent cannot control or bypass the Router. The Router verifies inputs and enforces outcomes; a policy engine decides. (Trust model)
- **Bridge trust:** The Router normally verifies a token from a trusted Bridge rather than parsing Passport credentials. (Router as verifier)
- **Actual target:** Policy applies to the resolved server, operation, and resources, not just the name the caller used. (Target resolution)
- **Policy inputs:** The engine receives required fields and declared optional inputs, with unverified values labeled. It receives no raw credentials. (Policy request)
- **Decision handling:** Only an enforceable permit allows dispatch. Other outcomes stop the call; failed obligations block dispatch or result release at their stage. (Policy response; Obligations)
- **Timed obligations:** Conditions apply before dispatch, before result release, or after the call. A condition meant to prevent an action must be met before dispatch. (Obligations)
- **Decision binding:** The Router dispatches the evaluated operation and arguments to the evaluated target, using a fresh decision at most once. (Decision binding)
- **Downstream credentials:** After a permit, an adapter obtains or applies a tool credential, narrows it where possible, and keeps it from the agent. (Downstream credentials)
- **Revocation:** Revocation may stop a decision, delegation, session, agent, or principal. The Router checks it again before dispatch. (Revocation and termination)
- **Limits:** Authority limits use verified keys, span delegated work where required, and share counters across Router instances. (Limits and state)
- **Composition:** Multiple Routers or decision sources may each stop a call; none can override another's refusal. (Placement and composition)

## Reading guide

Start with Scope and Trust model, then follow the payment example in Request flow. Policy request, Policy response, and Obligations define the policy exchange. Changes to ODIS lists the proposed specification changes.
