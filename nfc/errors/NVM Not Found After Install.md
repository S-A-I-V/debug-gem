---
tags: [nfc, error, environment]
date: 2026-04-20
severity: 🟡 Medium
status: resolved
---
# NVM Not Found After Install

## Error
```
command not found: nvm
```

## Root Cause
Shell hasn't loaded NVM path. The curl installer adds export lines to shell profile, but the current session doesn't pick them up.

## Fix
```bash
# Option 1: Restart terminal completely (close + reopen)

# Option 2: Source the profile
source ~/.zshrc    # or source ~/.bashrc

# Verify
nvm --version
```

## Time to Fix
~1 min.

## Connected To
- [[NFC-Environment]]
