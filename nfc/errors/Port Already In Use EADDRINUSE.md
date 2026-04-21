---
tags: [nfc, error, maf, docker, port]
date: 2026-04-20
severity: 🟠 High
status: resolved
---
# Port Already In Use EADDRINUSE

## Error
```
listen EADDRINUSE: address already in use :::6100
Error: listen EADDRINUSE: address already in use :::6100
    at Server.setupListenHandle [as _listen2] (node:net:1937:16)
```

## Root Cause
Another process is already using port 6100 (or 8080). Usually a leftover MAF-CLI or backend process from a previous session.

## Fix
```bash
kill -9 $(lsof -ti:6100)
kill -9 $(lsof -ti:8080)

# Retry
maf-cli start
```

## Time to Fix
~30 sec.

## Connected To
- [[MAF-CLI]]
