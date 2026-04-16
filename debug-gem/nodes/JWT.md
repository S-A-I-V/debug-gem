---
tags: [security, auth, token]
aliases: [jwt, json-web-token]
---
# JWT (JSON Web Token)

Compact, self-contained token for transmitting claims between parties. Base64url encoded, optionally signed and/or encrypted.

## Known For These Error Patterns
- [[State & Lifecycle Errors]] — Token expired (`exp` claim), not yet active (`nbf` claim)
- [[Configuration & Environment Errors]] — Wrong signing key, wrong algorithm, audience/issuer mismatch
- [[Serialization & Encoding Errors]] — Malformed token, truncated, URL encoding issues

## Common Gotchas
- `alg: none` vulnerability — always validate algorithm with allowlist
- Payload is NOT encrypted — anyone can decode it. Don't put secrets in JWT.
- Can't be revoked before expiry (stateless) — use short expiry + refresh tokens
- Clock skew between services — add `clockTolerance`
- Token size can exceed cookie limit (4KB)

## Related
- [[OAuth]], [[OIDC]] — JWT is the common token format
- [[SAML]] — XML-based alternative
- [[MOC — Security & Auth]]

## My Notes

