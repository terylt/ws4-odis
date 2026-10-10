# Downstream credentials

Most tools do not understand ODIS tokens. They require a credential issued for their own access system, such as an OAuth token, cloud role, or API key. The credential used for a permitted call must remain under Router or provider adapter control.

## Provider adapter

The Bridge's provider adapter uses Bridge-recorded authority to obtain credentials accepted by a downstream system. It may exchange the Bridge token, or a derivative, with that system's identity provider. In a gateway, the adapter may run alongside the Router. In a separate deployment, the Router may request the credential from the adapter, or an adapter later in the path may attach it. The exchange remains tied to a permitted call.

For a native-mode tool that verifies ODIS credentials itself, no exchange is needed. The agent's Passport credential and delegation pass through for the tool to verify.

## Decision order

The Router obtains a policy permit before the adapter issues or applies a downstream credential for that call. Exchanging one earlier would leave a usable credential for a call that might be denied or later reused without evaluation. The current ODIS gateway pattern lists exchange before policy evaluation; this document specifies the reverse order.

## Scope and custody

The Bridge token covers the agent's task; a policy decision covers one call. As far as the tool's access system allows, the downstream credential is limited to the target tool, the operation's required permissions, and a short lifetime. A credential for the invoice payment, for example, should not permit vendor administration or ledger access.

If the tool cannot express limits that narrow, the Router or adapter retains the broader credential and uses it only for the evaluated call. The agent never receives, stores, or presents a downstream credential; otherwise it could bypass the Router with an unevaluated call.

## Reuse

An adapter may cache a credential, but every call using it requires its own permit and must fit both the credential's limits and that permit. The adapter discards the credential when any authority it depends on is revoked and never retains it beyond the deployment's declared revocation delay. Reuse avoids another exchange, not another decision.

## Audit linkage

The component performing the final exchange records the delegation, tool, and credential used, as ODIS already requires. Its record and the Router's decision record should share the `evaluation_id`, linking authorization to the credential presented downstream.
