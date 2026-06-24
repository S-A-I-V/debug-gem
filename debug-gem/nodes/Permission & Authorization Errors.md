---
tags: [error-pattern, universal-failure-pattern]
aliases: [permission-errors, authorization-errors, access-denied, forbidden]
---
# Permission & Authorization Errors

> You don't have permission to do that. The system knows who you are but won't let you perform the action.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. The identity is established but the access is denied — wrong role, missing permission, restricted resource.

## The Pattern
The system enforces access control and the requestor doesn't have sufficient privileges. This includes:
- Authentication failures (who are you?) — 401
- Authorization failures (are you allowed?) — 403
- Resource-level restrictions (this specific resource is protected)
- Infrastructure-level blocking (file permissions, security groups)

## Universal Symptoms
- `401 Unauthorized` — not authenticated (bad/missing credentials)
- `403 Forbidden` — authenticated but not authorized
- `AccessDeniedException`, `PermissionDenied`, `PERMISSION_DENIED`
- `EACCES` — file system permission denied
- `Operation not permitted` (Linux)
- `CORS error` — browser-enforced cross-origin block
- `insufficient_scope`, `access_denied` (OAuth)

## Diagnostic Decision Tree
1. **IS IT AUTHENTICATION OR AUTHORIZATION?** 401 = identity problem, 403 = permission problem
2. **WHO is making the request?** Which identity/role/service account?
3. **WHAT are they trying to do?** Which action/operation/API call?
4. **ON WHAT resource?** Which specific resource (file, API endpoint, DB table)?
5. **WHERE is the policy?** Application code, IAM, RBAC, file system, network?
6. **DID IT WORK BEFORE?** Token expired? Role removed? Policy changed?

## Common Causes

### Authentication (401)
- Token/API key expired or revoked
- Token not included in request (missing header)
- Wrong auth scheme (Bearer vs Basic)
- Credentials rotated, client using old ones
- Service account key not configured

### Authorization (403)
- Missing IAM role/policy
- Wrong scope in OAuth token
- Resource-based policy denies access
- CORS blocking cross-origin request from browser
- IP allowlist doesn't include client

### File System
- Wrong file/directory ownership (`chown`)
- Wrong permission bits (`chmod`)
- SELinux/AppArmor blocking
- Container running as non-root without proper permissions

### Infrastructure
- Security group / firewall rule missing
- Kubernetes RBAC `Forbidden`
- Network Policy blocking
- Registry authentication for private images

## Fix Strategies
1. **Identify the actor** — who/what is making the request?
2. **Identify the requirement** — what permission is needed?
3. **Grant minimum needed** — principle of least privilege
4. **Check the policy chain** — multiple policies may apply (user → role → resource → SCP → boundary)
5. **Test in isolation** — use IAM policy simulators, test with known-good credentials

## Common Traps
- CORS is browser-enforced — server must set `Access-Control-Allow-Origin`. Postman works but browser doesn't.
- 401 vs 403 confusion — many APIs return 403 for everything (leaking less info)
- IAM policy evaluation is complex — explicit Deny always wins over Allow
- Service accounts in containers — pod needs the right service account annotation
- `sudo` vs proper permissions — don't fix with sudo, fix the actual permission

## Technologies That Commonly Produce This
- [[AWS]], [[GCP]], [[Azure]] — IAM, service accounts, resource policies
- [[Kubernetes]] — RBAC, NetworkPolicy, pod security
- [[OAuth]], [[OIDC]] — scope, consent, token issues
- [[RBAC]], [[ABAC]], [[PBAC]] — access control models
- [[Docker]] — daemon socket, registry auth
- [[Linux]] — file permissions, SELinux, AppArmor
- [[Git]], [[GitHub Actions]] — GITHUB_TOKEN scope, protected branches
- [[REST API]] — 401/403, CORS
- [[PostgreSQL]], [[MySQL]] — GRANT permissions, pg_hba.conf
- [[Solidity]] — onlyOwner, access control modifiers

## Related Patterns
- [[Connection & Network Errors]] — firewall blocking looks like network error but is permission
- [[Configuration & Environment Errors]] — wrong credentials configured
- [[State & Lifecycle Errors]] — expired token is both state and permission issue

## My Notes

