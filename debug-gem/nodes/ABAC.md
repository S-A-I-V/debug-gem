---
tags: [security, auth, access-control]
aliases: [abac, attribute-based-access-control]
---
# ABAC (Attribute-Based Access Control)

Access control based on attributes of the user, resource, action, and environment. More flexible than [[RBAC]] but more complex. Policies evaluate combinations of attributes at runtime.

## Known For These Error Patterns
- [[Permission & Authorization Errors]] — Policy evaluation denies access unexpectedly, attribute mismatch
- [[Configuration & Environment Errors]] — Missing attributes in request context, policy misconfiguration
- [[State & Lifecycle Errors]] — Stale attribute values (cached user attributes after role change)

## How ABAC Differs from RBAC
| RBAC | ABAC |
|------|------|
| Role → Permission | (Subject + Resource + Action + Environment) → Decision |
| Static assignments | Dynamic evaluation |
| Simple to audit | Complex to debug |
| Role explosion for fine-grained control | Handles fine-grained naturally |

## Common Gotchas
- Policy conflicts — multiple policies can apply to same request. Need conflict resolution strategy (deny-overrides, permit-overrides, first-applicable)
- Attribute freshness — if user department changed but cache hasn't updated, access decisions are wrong
- Policy debugging is hard — log the full attribute context with every deny decision
- Performance — complex policies with many attributes evaluated per request. Cache policy decisions where safe.
- Testing coverage — combinatorial explosion of attribute values makes exhaustive testing impossible

## Debug Flow
1. What attributes does the policy expect? (subject, resource, action, environment)
2. What attributes are actually present in the request context?
3. Which specific policy evaluated to Deny?
4. Is it a missing attribute or a wrong value?
5. Is the attribute source stale/cached?

## Implementations
- AWS Cedar — Amazon's policy language (used in Amazon Verified Permissions)
- OPA/Rego — Open Policy Agent, general-purpose
- XACML — XML-based, enterprise standard (complex)
- Casbin — Library-based, supports multiple models

## Related
- [[RBAC]], [[PBAC]] — alternative models
- [[OAuth]] — can provide attributes for ABAC decisions
- [[MOC — Security & Auth]]

## My Notes

