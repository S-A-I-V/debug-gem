---
tags: [nfc, error, registry, auth]
date: 2026-04-20
severity: 🔴 Critical
status: resolved
---
# npm 404 on Nielsen Packages

## Error
```
npm ERR! 404 Not Found - GET https://registry.npmjs.org/@nielsen-media%2fmaf-cli
```

## Root Cause
npm is looking on the public registry instead of Nielsen's private GitLab registry. Either `~/.npmrc` doesn't exist, has wrong URLs, or the PAT is invalid/expired.

## Fix
Checklist:
1. Does `~/.npmrc` exist? → `cat ~/.npmrc`
2. Are both registry URLs present?
3. Is the `glpat-` token correct and not expired?
4. Were ALL scopes selected when creating the token?

If any fail, recreate `~/.npmrc` using the template in [[NFC-Registry]].

## Time to Fix
~10 min.

## Connected To
- [[NFC-Registry]]
