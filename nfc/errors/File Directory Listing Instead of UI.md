---
tags: [nfc, error, maf, symlink]
date: 2026-04-20
severity: 🔴 Critical
status: resolved
---
# File Directory Listing Instead of UI

## Error
No error message. Browser shows a raw file/folder listing instead of the NFC application UI.

## Root Cause
MAF-CLI discovers microfrontends from `~/Documents/EMP/apps`. The symlink is either missing, pointing to the wrong directory, or the frontend hasn't been built.

## Fix
```bash
# 1. Check symlink
ls -la ~/Documents/EMP/apps
# Should show: nfc -> /path/to/frontend/build/nfc

# 2. If missing or wrong, recreate
rm -f ~/Documents/EMP/apps/nfc
mkdir -p ~/Documents/EMP/apps

# 3. Build frontend first
cd ~/Documents/code/nfc/frontend
yarn build

# 4. Create symlink
ln -s ~/Documents/code/nfc/frontend/build/nfc ~/Documents/EMP/apps
```

## Time to Fix
~3 min.

## Connected To
- [[MAF-CLI]]
- [[NFC-Frontend]]
