# Discovery

Tool lists shape what an agent may attempt and reveal what a deployment offers. When discovery passes through the Router, the list is filtered for the caller before release.

## Authority filtering

The Router includes only tools the agent could plausibly use. It filters with verified Bridge authority, permitted tools in the agent's registration, and each tool's resolved identity. Aliases for the same tool receive the same treatment.

Filtering is necessarily coarse: a later decision may depend on arguments, such as a payment amount, that are absent from the list request. A listed tool may therefore still be denied when called. The filter removes tools for which the caller clearly lacks authority, reducing fruitless attempts and unnecessary disclosure of the tool inventory.

## Call authorization

Discovery is not an authorization decision for later calls. An agent may remember or guess a hidden tool name. The Router verifies and decides every invocation independently, whether or not that tool appeared in a list.

The list request itself passes through the Router. The Router verifies the caller's token before answering and records the request, so invalid callers receive no tool inventory and requests remain attributable.

## Tool descriptions

Descriptions often come from the tools and may be inaccurate, stale, or written to redirect the model. The Router can confirm that a description came from the resolved tool but does not treat its content as verified. Inspecting descriptions for misleading instructions is content inspection, outside this document's scope; deployments may add it.

## List updates

The Router updates the offered list when authority changes through renewal or revocation, or when tools are added or removed. Where the protocol supports change notifications, it uses them to prompt the agent to fetch the list again.
