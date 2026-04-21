---
tags: [nfc, error, security]
date: 2026-04-20
severity: 🔴 Critical
status: resolved
---
# npmrc Token Committed to Git

## Error
No error message. Credentials silently exposed in Git history.

## Root Cause
A project-level `.npmrc` containing `glpat-` token was committed to the repository.

## Fix
```bash
# 1. Immediately rotate the token
# GitLab → Profile → Access Tokens → Revoke old → Create new

# 2. Remove from Git
git rm --cached .npmrc
echo ".npmrc" >> .gitignore
git commit -m "Remove .npmrc from tracking"

# 3. Use global ~/.npmrc only (never project-level with tokens)
```

## Time to Fix
~15 min (including token rotation).

## Connected To
- [[NFC-Registry]]
