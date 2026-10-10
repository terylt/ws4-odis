# Target resolution

The Router resolves a call to its actual target before requesting a policy decision. The decision must cover the operation and resources that will be reached.

## Names and aliases

A gateway may expose the same payment tool as `finance__erp` or `erp_payments`, while unrelated servers may each offer a tool named `search`. Policy based on a caller-visible name could miss an alias or conflate different tools. The Router therefore uses the resolved target for policy evaluation.

## Resolved target

For each call, the Router determines:

- **Operation type:** a tool invocation, resource read, prompt fetch, or other operation the binding supports.
- **Server:** the server that will handle the call, identified through trusted routing information.
- **Tool and action:** the tool or endpoint and the action it performs. In the payment example, `finance__erp` resolves to the ERP payment tool and its invoice-payment action.
- **Resources:** every resource the call affects. The `invoice_id` argument identifies invoice 4411; a create operation identifies the destination collection. The Router does not substitute a wildcard for resources it cannot identify.
- **Gateway or namespace:** the entry point, if present, because a deployment may grant access to different virtual servers.

For a web API, the Router uses the method, address, and relevant fields to resolve the service, action, and affected resources.

## Resolution sources

When the Router runs in the gateway, the gateway's routing configuration determines both the target evaluated and the target invoked. If they run separately, the gateway provides a trusted mapping or a signed statement of the target resolved for each call. In either case, the evaluated target must match the actual destination.

A trusted tool or service registry may supplement that mapping. Protocol-specific *bindings* define how calls map to policy actions and resources, including which arguments identify resources or matter to the decision.

The agent supplies the name and arguments, but cannot supply the mapping used to interpret them. Otherwise it could present a sensitive call as a harmless one.

## Target attributes

Policies may use a target's owner, data sensitivity, or storage location. Each attribute records its source, verification status, observation time, and validity period. Registry attributes from a trusted source are verified. A tool's self-description, such as a read-only label, remains unverified; the Router may pass it to the policy engine with that status.

## Resolution failure

The Router denies a call if it cannot resolve the name, server, or affected resources, or if resolution would require an unknown target or wildcard. It does not infer a safe target from incomplete information.
