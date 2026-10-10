# Open questions

The following decisions remain outside this document. References identify the affected sections or ODIS issues.

- **Result governance:** Should ODIS govern results generally, beyond obligations on one call, given the overlap with content inspection? (Scope; Obligations)
- **Sessions:** What defines a session, and how is it identified for limits, revocation, and audit? (Limits and state; Revocation and termination; Audit; #25)
- **Client application identity:** A coding assistant or similar application may sign in separately from the agent and principal. Should its identity appear in the policy request, marked unverified until ODIS defines a check? (Policy request; #13)
- **Sandbox identity:** Should a sandbox and the agents or sub-agents it hosts have separate identities? Which identity does the Router verify? (Router as verifier)
- **Default arguments:** Should a minimum set of call arguments always reach the policy engine, rather than only those declared by a policy? (Policy request)
- **Delegation history:** The field table recommends delegation depth and sends the full chain only when declared. Should depth or organizational hops be required by default? (Policy request)
- **Common obligations:** Should ODIS define portable obligation types with fixed handling for approval, field removal, and notification? (Obligations)
- **Policy-engine chaining:** The Router stops on a final `not_applicable` response. Is it acceptable for deployments to finish chaining engines before returning one response to the Router? (Policy response)
- **Decisions across Routers:** May one Router pass a signed decision to another instead of each deciding independently? How would the decision be protected and bound to the call? (Placement and composition)
- **Evaluation coverage:** Must every call receive a policy evaluation? The current ODIS basic profile does not require this. (Changes to ODIS; #23)
- **Registration checks:** Must the Passport issuer read the registration record? The Bridge resolves it either way, so the Router input is unchanged, but the agent credential may differ. (Router as verifier; #33)
