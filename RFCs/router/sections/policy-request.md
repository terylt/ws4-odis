# Policy request

After verifying the caller and resolving the target, the Router sends a decision request to a policy engine.

## Request content

The request carries facts established by the Router and identifies any unverified inputs. The policy engine need not parse Passport credentials, Bridge tokens, or their supporting proof. It receives no raw credentials.

Inputs are grouped by policy concern:

- **Agent:** identity, running instance, owner, and policy profile.
- **Principal:** the person or service represented, plus declared attributes such as roles or department.
- **Authority:** actions, resources, and constraints remaining after all delegation limits, as recorded by the Bridge.
- **Scope:** the task and, once defined by ODIS, the session.
- **Target:** the resolved operation, resources, and target attributes.
- **Environment:** current conditions, including risk signals labeled by verification status.

## Input minimization

The Router includes mandatory fields and only the optional inputs needed for the decision. Arguments, the delegation chain, task description, and software digest may contain sensitive information and are sent only when declared. Raw credentials, private keys, and secrets are never sent.

A deployment publishes a policy input declaration for each engine. It names the optional fields and argument paths the engine receives, their sources, and required verification status. Paths such as `amount` or `payment.currency` let the Router select individual arguments. A test can check that the request contains only required fields and declared optional inputs.

If a required input is unavailable or cannot meet its declared verification level, the Router denies the call.

## Field inventory

Field names are provisional. "When declared" fields appear only if the policy input declaration requests them.

| Field | Required | Description |
|---|---|---|
| `schema_version` | Yes | Request format version |
| `evaluation_id` | Yes | Router-assigned unique decision ID, copied in the response |
| `request_trace_id` | Yes | Cross-component log correlation; never identity or authority evidence |
| `request_timestamp` | Yes | Router receipt time; never agent-supplied |
| **agent** | | |
| `agent.agent_id` | Yes | Agent resolved through registration |
| `agent.runtime_instance_id` | Yes | Running agent instance |
| `agent.registration_record_ref` | Yes | Checked registration version |
| `agent.policy_profile_ref` | Yes | Assigned policy profile |
| `agent.owner_ref` | Recommended | Owning team, application, or business unit |
| `agent.software_hash` | When declared | Running software digest |
| **principal** | | |
| `principal.id` | Yes | Person or service the agent represents |
| `principal.type` | Yes | `human` or `service` |
| `principal.authentication_context` | When declared | How and when the principal signed in |
| `principal.attributes` | When declared | Facts about the principal, such as roles, groups or department, each with its source and whether it was verified. The declaration names which attributes are sent |
| **authority** | | |
| `authority.delegation_id`, `authority.issuer` | Yes | Delegation ID and issuing Bridge |
| `authority.actions` | Yes | Allowed actions after all limits |
| `authority.resources` | Yes | Allowed resources after all limits |
| `authority.constraints` | Yes | Applicable conditions, such as a payment limit |
| `authority.expires_at` | Yes | Earliest applicable expiry |
| `authority.attenuation_profile_ref` | Yes | Interpretation of actions, resources, and constraints |
| `authority.delegation_depth` | Recommended | Number of delegation steps from the principal |
| `authority.delegation_chain` | When declared | The full history of how authority was passed on |
| **scope** | | |
| `scope.task_id` | Yes | Task containing the call |
| `scope.session_id` | Once defined | Session containing the call |
| `scope.task_description` | When declared | Readable task description |
| **target** | | |
| `target.operation_kind` | Yes | Kind of operation defined by the binding, such as tool invocation, resource read, or prompt fetch |
| `target.called_tool` | Yes | Caller-visible tool name |
| `target.namespace` | If used | Gateway namespace or virtual server |
| `target.upstream_server` | Yes | Server handling the call |
| `target.upstream_tool` | If applicable | Tool on that server |
| `target.operation` | Yes | Operation within the tool or service |
| `target.resources` | Yes | Every affected resource; never a wildcard |
| `target.attributes` | Optional | Target facts with source and verification status |
| `target.arguments` | When declared | Requested arguments |
| **environment** | | |
| `environment.runtime_risk_signals` | Optional | Risk signals, each with its source and whether it was verified |

A deployment can add its own fields as extensions, each with a versioned definition. A policy engine ignores extensions it does not use, unless the policy input declaration says the decision depends on them.

## Example

The payment in Request flow produces a request such as this, with required fields and the finance policy's declared optional inputs:

```json
{
  "schema_version": "1",
  "evaluation_id": "ev_7f3c9a",
  "request_trace_id": "tr_51b2e0",
  "request_timestamp": "2026-09-30T14:02:11Z",
  "agent": {
    "agent_id": "agents/invoice-reconciler",
    "runtime_instance_id": "ri_04d1",
    "registration_record_ref": "registry.example.com/reg_19@7",
    "policy_profile_ref": "profiles/finance-agents",
    "owner_ref": "teams/finance-ops"
  },
  "principal": {
    "id": "users/alice",
    "type": "human",
    "attributes": {
      "roles": { "value": ["finance-clerk"], "source": "https://idp.example.com", "verified": true }
    }
  },
  "authority": {
    "delegation_id": "dl_88a0",
    "issuer": "https://bridge.example.com",
    "actions": ["invoices:read", "invoices:pay"],
    "resources": ["https://erp.example.com"],
    "constraints": { "max_amount": 1000, "currency": "USD" },
    "expires_at": "2026-09-30T14:30:00Z",
    "attenuation_profile_ref": "https://example.com/profiles/finance/v1",
    "delegation_depth": 1
  },
  "scope": { "task_id": "task_reconcile_q3" },
  "target": {
    "operation_kind": "invoke",
    "called_tool": "finance__erp",
    "namespace": "vs/finance-tools",
    "upstream_server": "https://erp.example.com/mcp",
    "upstream_tool": "erp",
    "operation": "invoices.pay",
    "resources": ["https://erp.example.com/invoices/4411"],
    "attributes": {
      "classification": { "value": "confidential", "source": "https://registry.example.com", "verified": true }
    },
    "arguments": { "amount": 250, "currency": "USD" }
  }
}
```

The declaration selects amount, currency, and Alice's roles. Invoice 4411 appears as a resolved resource rather than a raw argument.
