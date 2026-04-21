---
tags: [nfc, error, frontend, symlink]
date: 2026-04-20
severity: 🟠 High
status: resolved
---
# Symlink Points to Wrong Build

## Error
App loads but shows old/wrong version, or shows file directory listing.

## Root Cause
Symlink at `~/Documents/EMP/apps/nfc` points to wrong directory. Common after moving the project folder or changing branch.

## Fix
```bash
# Check current symlink target
ls -la ~/Documents/EMP/apps

# Remove and recreate
rm -f ~/Documents/EMP/apps/nfc
cd ~/Documents/code/nfc/frontend
yarn build
ln -s $(pwd)/build/nfc ~/Documents/EMP/apps
```

## Time to Fix
~2 min.

## Connected To
- [[NFC-Frontend]]
- [[File Directory Listing Instead of UI]]
