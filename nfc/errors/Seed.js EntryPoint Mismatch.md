---
tags: [nfc, error, backend, config]
date: 2026-04-20
severity: 🟠 High
status: resolved
---
# Seed.js EntryPoint Mismatch

## Error
MAF-CLI loads but API calls return 404 or connection refused. Frontend renders but can't fetch data.

## Root Cause
`seed.js` has `entryPoint` set to `localhost:{port}` but MAF-CLI runs inside Docker. Docker can't reach the host's `localhost`.

## Fix
In `seed.js`, change:
```javascript
// Wrong
entryPoint: "http://localhost:3001"

// Correct
entryPoint: "http://host.docker.internal:3001"
```

`host.docker.internal` is Docker's way of reaching the host machine from inside a container.

## Time to Fix
~1 min.

## Connected To
- [[NFC-Backend]]
- [[MAF-CLI]]
