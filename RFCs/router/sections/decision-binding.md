# Decision binding

The Router acts on a policy decision only for the call evaluated and while that decision remains valid.

## Evaluation identifiers

The Router assigns each policy request a unique `evaluation_id`. The policy engine copies it into the response, and the Router accepts the response only for the matching pending evaluation.

The `request_trace_id` serves a different purpose: it correlates logs across components. A request may produce several evaluations, for example after a retry or at multiple Routers, so the trace ID cannot identify one decision.

## Request integrity

The Router retains the incoming call unchanged and derives the policy request from it. Before dispatch, it confirms that the operation, arguments, resolved target, and route still match what was evaluated. If any of these or the permit's obligations change, the Router obtains a new decision. An approval obligation applies only to the evaluated call.

The Router may add a downstream credential after approval, but it does not change the evaluated operation or arguments. A digest of arguments may detect a change, but it cannot by itself bind a decision to the target, route, and retained call.

## Freshness and reuse

The response includes `issued_at` and `expires_at`; the Router acts on it only within that interval and at most once. An expired or previously used response cannot authorize dispatch. The Router still performs current-state checks, including revocation, before sending the call. A short decision lifetime does not replace those checks.

## External policy engines

An in-process engine shares the Router's trust boundary. When the engine runs as a separate service, both sides authenticate each other and protect the request and response against modification. Mutually authenticated TLS is one option; verified HTTP Message Signatures are another and can preserve a signed decision as evidence.

## Decision records

The Router records the evaluation ID, the request and resolved target evaluated, the response, and the resulting action. These records link a dispatched call to the decision that permitted it and show whether the dispatched operation and arguments matched those evaluated.
