---
tags: [nfc, error, environment]
date: 2026-04-20
severity: 🔴 Critical
status: resolved
---
# Homebrew Node Breaks Docker Mounts

## Error
```
Docker image cannot be mounted
```
`maf-cli start` fails immediately.

## Root Cause
Homebrew installs Node.js to `/opt/homebrew` (Apple Silicon) or `/usr/local` (Intel). MAF Docker images use hardcoded paths that can't resolve Homebrew locations. Adding `/opt/homebrew` to Docker File Sharing does NOT fix it.

## Fix
```bash
brew uninstall node
brew uninstall nvm
brew cleanup

# Reinstall NVM via curl (NOT Homebrew)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.zshrc

nvm install --lts
nvm alias default node
```

## Time to Fix
First time: ~45 min. With this doc: ~5 min.

## Connected To
- [[NFC-Environment]]
- [[MAF-CLI]]
