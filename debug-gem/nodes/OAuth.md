---
tags: [security, auth, protocol]
aliases: [oauth, oauth2]
---
# OAuth 2.0

Authorization framework. Lets users grant third-party apps limited access without sharing passwords.

## Known For These Error Patterns
- [[State & Lifecycle Errors]] — `invalid_grant` (token expired/revoked), `interaction_required`
- [[Configuration & Environment Errors]] — `invalid_client`, `redirect_uri_mismatch`
- [[Permission & Authorization Errors]] — `invalid_scope`, `access_denied`, `insufficient_scope`

## Common Gotchas
- Never store tokens in localStorage (XSS vulnerable) — use httpOnly cookies
- Public clients (SPAs, mobile) MUST use PKCE
- Always use `state` parameter to prevent CSRF
- Token rotation race condition — concurrent requests with old refresh token fail

## Related
- [[OIDC]] — identity layer on top of OAuth
- [[JWT]] — common token format
- [[SAML]] — enterprise alternative
- [[MOC — Security & Auth]]

## My Notes

