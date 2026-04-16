---
tags: [security, auth, identity, enterprise]
aliases: [saml]
---
# SAML (Security Assertion Markup Language)

XML-based authentication protocol. Common in enterprise SSO. IdP (Identity Provider) authenticates, SP (Service Provider) trusts.

## Known For These Error Patterns
- [[Configuration & Environment Errors]] — Certificate mismatch, audience restriction, entity ID mismatch
- [[State & Lifecycle Errors]] — Assertion expired (clock skew), InResponseTo mismatch
- [[Serialization & Encoding Errors]] — XML canonicalization issues breaking signatures

## Common Gotchas
- XML signature wrapping attacks — validate signature covers the assertion
- Clock skew between IdP and SP breaks assertion validation
- Metadata (certificates, endpoints) must be kept in sync

## Related
- [[OAuth]], [[OIDC]] — modern alternatives
- [[MOC — Security & Auth]]

## My Notes

