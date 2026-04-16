---
tags: [security, auth, access-control]
aliases: [rbac, role-based-access-control]
---
# RBAC (Role-Based Access Control)

Access control model where permissions are assigned to roles, and roles are assigned to users. Most common authorization model.

## Debug Flow
User → has Roles → Roles have Permissions → Permission matches required action?

## Common Gotchas
- Role explosion — too many fine-grained roles become unmanageable
- Different role assignments per environment ([[The Environment Delta]])

## Related
- [[ABAC]], [[PBAC]] — alternative access control models
- [[Permission & Authorization Errors]] — the pattern RBAC produces
- [[MOC — Security & Auth]]

## My Notes

