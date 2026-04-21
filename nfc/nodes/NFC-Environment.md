---
tags: [nfc, environment, nvm, setup]
aliases: [nfc-env, nfc-setup]
---
# NFC-Environment

Local dev environment setup. NVM-managed Node.js is mandatory — Homebrew Node breaks MAF-CLI Docker mounts.

## Errors Logged
- [[Homebrew Node Breaks Docker Mounts]]
- [[NVM Not Found After Install]]
- [[Husky Git Not Found on Yarn Install]]

## Required Versions
| Tool | Min Version | Install |
|---|---|---|
| Node.js | 18.x LTS | `nvm install --lts` |
| Yarn | 1.22.x | `npm install -g yarn` |
| Nodemon | 2.x | `npm install -g nodemon` |
| MAF-CLI | Latest | `npm install -g @nielsen-media/maf-cli` |

## Related
- [[NFC-Registry]] — npm registry config needed before MAF-CLI install
- [[MAF-CLI]] — the Docker container that requires NVM-installed Node
- [[NFC]]

## My Notes

