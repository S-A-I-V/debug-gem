---
tags: [security, auth, access-control]
aliases: [pbac, policy-based-access-control]
---
# PBAC (Policy-Based Access Control)

Access control using policy languages (Rego/OPA, Cedar, Casbin). Policies are code, evaluated at runtime. Externalizes authorization logic from application code.

## Known For These Error Patterns
- [[Permission & Authorization Errors]] — Policy denies access, missing policy rule, default-deny catches unexpected requests
- [[Configuration & Environment Errors]] — Policy engine not reachable, policy bundle out of sync, wrong policy version deployed
- [[State & Lifecycle Errors]] — Policy data stale (external data source not refreshed), policy bundle deployment lag

## Key Implementations

### OPA (Open Policy Agent) + Rego
- `undefined decision` — policy doesn't cover this input. OPA returns undefined, app interprets as deny.
- `rego_type_error` — type mismatch in policy logic
- `bundle download failed` — OPA can't fetch latest policy bundle from server
- **Debug**: Use `opa eval` locally to test policy with sample input

### AWS Cedar
- `EntityNotFound` — referenced entity doesn't exist in entity store
- Policy validation catches errors at authoring time — strong type system
- **Debug**: Use Cedar CLI `cedar authorize` to test decisions locally

### Casbin
- Model definition mismatch with policy rules
- Adapter (storage) connection failures
- **Debug**: Use Casbin online editor to test model + policy + request

## Common Gotchas
- Default-deny vs default-allow — always default to deny, but this means every new endpoint needs explicit policy
- Policy deployment lag — code deploys new endpoint, policy not yet updated = users locked out
- Policy testing — unit test policies separately from application logic
- Performance — policy evaluation per request adds latency. Use decision caching with appropriate TTL.
- Data freshness — if policy references external data (user roles, resource ownership), stale data = wrong decisions

## Debug Flow
1. What was the exact input to the policy engine? (subject, action, resource, context)
2. Which specific policy rule evaluated? (enable decision logging)
3. Was it an explicit deny or undefined/no-match (default deny)?
4. Is the policy bundle up-to-date? When was last sync?
5. Is external data (user attributes, resource metadata) fresh?

## Related
- [[RBAC]], [[ABAC]] — alternative models (PBAC can implement either)
- [[Kubernetes]] — uses OPA/Gatekeeper for admission control
- [[MOC — Security & Auth]]

## My Notes

