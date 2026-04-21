---
tags: [nfc, ssl, certificates, https]
aliases: [nfc-ssl, nfc-certs]
---
# NFC-SSL

Self-signed SSL certificate handling. MAF-CLI serves assets over HTTPS — browsers block by default.

## Errors Logged
- [[Blank Page After Login SSL Rejection]]

## The Fix Workflow
```
DevTools → Network tab → find red .js requests →
right-click each → Open in new tab → Advanced →
Proceed to localhost (unsafe) → repeat per port → refresh app
```

## Key Facts
- Each port needs its own exception (8080, 3000, 3001 are different origins)
- Blank page with no console errors = SSL exceptions not accepted
- Exceptions may reset after browser updates
- This is expected behavior, not a bug

## Related
- [[MAF-CLI]] — generates and serves the self-signed certs
- [[NFC-Frontend]] — assets that fail to load without exceptions
- [[NFC]]

## My Notes

