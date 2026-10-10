# Router as verifier

Before seeking a policy decision, the Router establishes the caller's identity and authority and verifies that the caller is the credential holder. This section distinguishes checks made when a token is issued from checks required on each call.

## Required facts

For each call, the Router must establish:

1. the agent and its running instance;
2. the principal for whom it acts;
3. its authority for this call; and
4. that the caller holds the agent's private key.

The fourth fact prevents a copied token from being used by another holder. The agent signs part of the request with its private key, and the Router verifies the signature against the key identified in the token. This is proof of possession. ODIS does not require a particular artifact to carry these facts, provided the Router can verify them.

## Credential sources

Typically, the Bridge verifies the agent's Passport credential, determines its authority, and issues a token containing the facts the Router needs. For the Bridge token to stand in for the Passport credential:

1. The Bridge must copy the required Passport conclusions into its signed token.
2. The token must be bound to the same private key as the Passport credential.
3. The token must expire no later than the Passport credential.
4. Revoking the Passport credential must also revoke tokens derived from it.

The Router can then verify Bridge tokens without parsing Passport credentials, as described in Trust model. For a native-mode target that verifies ODIS tokens itself, the agent's Passport credential and delegation pass through to the target for verification.

## Token checks

For a call carrying a Bridge token, the Router checks:

- a valid signature from a Bridge configured as trusted;
- expiry and revocation status;
- the audience, which must include the Router or the target tool;
- proof of possession of the bound key; and
- that the call is within the token's task and resource limits.

## Check frequency

At issuance, the Bridge checks that the Passport credential is genuine, the agent's software was approved, and each delegation passed on no more authority than it received. The Router relies on those results in a token from a configured trusted Bridge. For a call from another organization, the receiving Bridge checks the incoming credentials and delegations and issues its own token. The Router still relies only on a Bridge it trusts.

Expiry, revocation, and the principal's continuing authority can change after issuance and require checks on every call. Revocation data may be cached within the deployment's declared revocation delay.

A single-use token must be consumed by exactly one component on the call path. The deployment identifies that component; other components verify the token without consuming it, so they do not reject it as a replay.

## Registration changes

The agent's organization maintains a registration with its owner, approved software, and applicable rules. Ordinary changes, such as approving new software, take effect when short-lived credentials are renewed, within one renewal period. This avoids immediate reissuance across a large population.

Suspension or compromise requires revocation events. The Router and other relying parties reject affected tokens within the declared revocation delay. The events may also need to reach other organizations that trust the agent.

## Failure handling

The Router denies the call if it cannot establish any required fact, complete a check, or interpret an input. This includes unavailable revocation data. An incomplete check never permits a call.
