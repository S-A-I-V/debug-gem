---
tags: [nfc, error, frontend, webpack]
date: 2026-04-20
severity: 🟡 Medium
status: resolved
---
# Webpack Watch Not Updating

## Error
You save a frontend file but the browser doesn't update. No error in terminal.

## Root Cause
Multiple possible causes, check in order:
1. `yarn run watch:{env}` not running
2. Symlink broken (MAF serving stale build)
3. Browser cache serving old version
4. SSL exception expired (new assets blocked silently)

## Fix
```bash
# 1. Is watch running? Check Terminal 2 for "webpack is watching the files..."

# 2. Is symlink valid?
ls -la ~/Documents/EMP/apps

# 3. Hard refresh browser
# Cmd+Shift+R (Mac) or Ctrl+Shift+R (Windows/Linux)

# 4. Check DevTools Network tab for red requests (SSL issue)
# If red requests → follow [[Blank Page After Login SSL Rejection]] fix
```

## Time to Fix
~2 min.

## Connected To
- [[NFC-Frontend]]
