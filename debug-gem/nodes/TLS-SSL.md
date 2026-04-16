---
tags: [security, networking, encryption, protocol]
aliases: [tls, ssl, tls-ssl, https]
---
# TLS/SSL

Transport Layer Security. Encrypts data in transit. HTTPS = HTTP + TLS.

## Known For These Error Patterns
- [[State & Lifecycle Errors]] — Certificate expired
- [[Configuration & Environment Errors]] — Hostname mismatch, self-signed cert, missing intermediate cert, wrong TLS version
- [[Connection & Network Errors]] — Handshake failure, protocol mismatch

## Debug
- `openssl s_client -connect host:443` — test TLS connection
- `openssl x509 -in cert.pem -text -noout` — inspect certificate
- `curl -v https://host` — verbose TLS handshake output
- ssllabs.com/ssltest — comprehensive online test

## Related
- [[DNS]] — domain resolution before TLS
- [[JWT]], [[OAuth]] — auth over TLS
- [[MOC — Networking & APIs]]
- [[MOC — Security & Auth]]

## My Notes

