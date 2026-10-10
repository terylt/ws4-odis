# Scope

## Background

ODIS separates agent identity and access into three layers. The Passport verifies the software and environment of a running agent, then issues a credential. The Bridge records the principal, task, and granted authority in a token. The Router checks each call to a tool or service against that authority and applicable policy.

A *principal* is the person or service for whom an agent acts. A *token* is a signed set of claims, such as an agent identity or permission. Token verification checks the issuer, expiry, revocation status, and that the presenter holds the key to which the token was bound. A *policy engine* applies an organization's rules to a call and returns a decision.

## Coverage

This document defines the Router's role in handling a call: verify its tokens, resolve the actual tool and resource, obtain a policy decision, and enforce it. It also covers rate limits, revocation, tool discovery, audit records, Router placement and composition, and the Router's dependencies. Sessions are referred to where they matter but are not yet defined by ODIS.

## Out of scope

Passport credential issuance and Bridge authority grants are defined elsewhere in ODIS. This document specifies only what the Router requires from them.

ODIS does not prescribe a policy language or engine. This document specifies the facts the Router supplies and the decisions it must handle.

General inspection of request and result content, including sensitive data in free text and instructions intended to redirect an agent, is outside the current ODIS scope. Deployments may add content inspection alongside the Router.

## Open question: result governance

The Router can enforce conditions on a result before releasing it, such as removing a field. ODIS has not yet defined general result governance. An authorized call can return excessive data or instructions intended to redirect the agent. Deciding which results require inspection, and how, overlaps with content inspection. The ODIS group must resolve whether the Router's responsibilities extend to results generally.
