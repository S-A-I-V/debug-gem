---
tags: [nfc, error, frontend, husky]
date: 2026-04-20
severity: 🟡 Medium
status: resolved
---
# Husky Git Not Found on Yarn Install

## Error
```
husky - .git can't be found (see https://typicode.github.io/husky/#/?id=custom-directory)
error Command failed with exit code 1.
```

## Root Cause
Husky expects `.git` at the directory where `yarn install` runs. NFC runs it from `frontend/` subdirectory, but `.git` is at project root.

## Fix
```bash
cd frontend
yarn install --ignore-scripts
```

## Time to Fix
~1 min.

## Connected To
- [[NFC-Frontend]]
