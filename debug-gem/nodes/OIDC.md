---
tags: [security, auth, identity, protocol]
aliases: [oidc, openid-connect]
---
# OIDC (OpenID Connect)

Identity layer on top of [[OAuth]] 2.0. Adds authentication (who you are) to OAuth's authorization (what you can do). Returns ID tokens with user identity claims.

## Known For These Error Patterns
- [[Configuration & Environment Errors]] — Issuer mismatch, wrong discovery URL, client ID/secret misconfiguration, redirect URI mismatch
- [[State & Lifecycle Errors]] — ID token expired, session timeout, nonce mismatch (replay protection)
- [[Permission & Authorization Errors]] — Insufficient scopes (`openid`, `profile`, `email`), consent not granted
- [[Connection & Network Errors]] — Discovery endpoint unreachable, JWKS endpoint timeout
- [[Serialization & Encoding Errors]] — Malformed ID token, invalid JWT signature

## Key Concepts
- **Discovery**: `/.well-known/openid-configuration` endpoint exposes all provider URLs
- **ID Token**: JWT containing user identity claims (`sub`, `email`, `name`, etc.)
- **UserInfo Endpoint**: Additional claims not in ID token
- **Flows**: Authorization Code (server apps), Authorization Code + PKCE (SPAs/mobile), Implicit (deprecated)

## Common Errors
- `invalid_client` — client ID doesn't exist or secret is wrong at token endpoint
- `interaction_required` — user must re-authenticate (session expired, consent needed)
- `login_required` — silent auth failed, user must log in interactively
- `invalid_grant` — authorization code expired or already used (codes are single-use)
- ID token `iss` doesn't match expected issuer — misconfigured provider URL
- ID token `aud` doesn't match client ID — token issued for different application
- `nonce` mismatch — potential replay attack or session state lost

## Common Gotchas
- ID tokens are for the CLIENT, not for APIs — don't send ID tokens as API bearer tokens (use access tokens)
- Always validate ID token signature using JWKS from discovery endpoint
- `sub` claim is the stable user identifier — `email` can change
- Token validation must check: signature, `iss`, `aud`, `exp`, `nonce`
- Clock skew between servers breaks token validation — add tolerance (30-60 seconds)
- Silent authentication (`prompt=none`) fails if third-party cookies blocked (Safari, Firefox)

## Debug Flow
1. Check discovery endpoint: `curl https://provider/.well-known/openid-configuration`
2. Verify client ID and redirect URI match provider configuration exactly
3. Decode ID token at jwt.io — check `iss`, `aud`, `exp`, `sub`
4. Verify token signature against JWKS endpoint keys
5. Check scopes requested vs scopes granted

## Related
- [[OAuth]] — OIDC is built on OAuth 2.0
- [[JWT]] — ID tokens are JWTs
- [[SAML]] — enterprise alternative (XML-based, more complex)
- [[MOC — Security & Auth]]

## My Notes

