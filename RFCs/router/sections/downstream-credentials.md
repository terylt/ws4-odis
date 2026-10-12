# Downstream credentials

Most tools do not understand ODIS tokens. They require a credential issued for their own access system, such as an OAuth token, cloud role, or API key. The credential used for a permitted call must remain under Router or provider adapter control.

## Provider adapter

The Bridge's provider adapter uses Bridge-recorded authority to obtain credentials accepted by a downstream system. It obtains them on its own credential or by exchanging a derivative of the Bridge token with that system's identity provider (see Bound tokens). In a gateway, the adapter may run alongside the Router. In a separate deployment, the Router may request the credential from the adapter, or an adapter later in the path may attach it. The exchange remains tied to a permitted call.

For a native-mode tool that verifies ODIS credentials itself, no exchange is needed. The agent's Passport credential and delegation pass through for the tool to verify.

## Bound tokens

A Bridge token bound to the agent's key can be used only by the agent, so the adapter cannot present it to another identity provider. The adapter instead requests the downstream credential on its own credential, naming the agent and principal the Router verified. Alternatively, the issuer may explicitly allow the adapter to exchange tokens bound to another key. In the first case the downstream identity provider relies on the adapter's statement, so it must trust the adapter for that purpose. In both cases the agent's key stays with the agent, and the downstream credential is bound to the component that presents it.

## Decision order

The Router obtains a policy permit before the adapter issues or applies a downstream credential for that call. Exchanging one earlier would leave a usable credential for a call that might be denied or later reused without evaluation. The current ODIS gateway pattern lists exchange before policy evaluation; this document specifies the reverse order.

## Scope and custody

The Bridge token covers the agent's task; a policy decision covers one call. As far as the tool's access system allows, the downstream credential is limited to the target tool, the operation's required permissions, and a short lifetime. A credential for the invoice payment, for example, should not permit vendor administration or ledger access.

If the tool cannot express limits that narrow, the Router or adapter retains the broader credential and uses it only for the evaluated call. The agent never receives, stores, or presents a downstream credential; otherwise it could bypass the Router with an unevaluated call.

## Reuse

An adapter may cache a credential, but every call using it requires its own permit and must fit both the credential's limits and that permit. The adapter discards the credential when any authority it depends on is revoked and never retains it beyond the deployment's declared revocation delay. Reuse avoids another exchange, not another decision.

## Audit linkage

The component performing the final exchange records the delegation, tool, and credential used, as ODIS already requires. Its record and the Router's decision record should share the `evaluation_id`, linking authorization to the credential presented downstream.
