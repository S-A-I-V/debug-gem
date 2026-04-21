---
tags: [nfc, error, registry, docker, auth]
date: 2026-04-20
severity: 🟠 High
status: resolved
---
# GitLab PAT Expired Docker Fails

## Error
```
HTTP Basic: Access denied
```
`docker login`, `docker pull`, and CI jobs suddenly fail.

## Root Cause
GitLab PATs have expiration dates. Once reached, GitLab revokes automatically. No warning before expiry.

## Fix
```bash
# 1. GitLab → Profile → Access Tokens → Create new PAT
#    Scopes: read_registry + write_registry (minimum)

# 2. Re-authenticate Docker
docker login registry.gitlab.com -u <your_gitlab_username>
# Paste new token when prompted

# 3. Update ~/.npmrc with new token (both _authToken lines)
```

## Time to Fix
~5 min.

## Connected To
- [[NFC-Registry]]
