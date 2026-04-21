---
tags: [nfc, maf, docker, cli]
aliases: [maf-cli, maf]
---
# MAF-CLI

Nielsen's Media Application Framework CLI. Docker container that handles routing, Okta authentication, and microfrontend assembly. The most failure-prone component in the NFC stack.

## Errors Logged
- [[Port Already In Use EADDRINUSE]]
- [[File Directory Listing Instead of UI]]
- [[Blank Page After Login SSL Rejection]]

## Key Facts
- Reads apps from hardcoded path: `~/Documents/EMP/apps` (not configurable)
- Must be started from `frontend/conf/dev-env/`
- Common ports: 6100, 8080

## Diagnostic
```bash
docker info > /dev/null 2>&1 && echo "Docker OK" || echo "Docker NOT running"
ls -la ~/Documents/EMP/apps
lsof -ti:6100 && echo "Port 6100 in use" || echo "Port 6100 free"
lsof -ti:8080 && echo "Port 8080 in use" || echo "Port 8080 free"
```

## Related
- [[NFC-Frontend]] — build output that MAF serves
- [[NFC-SSL]] — cert exceptions after MAF login
- [[NFC-Environment]] — NVM requirement for Docker mount compatibility
- [[NFC]]

## My Notes

