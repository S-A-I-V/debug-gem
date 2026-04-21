---
tags: [application, nfc, nielsen]
aliases: [nfc, nielsen-fulfillment-center]
---
# NFC — Nielsen Fulfillment Center

> Application error log and knowledge base. Every error encountered during NFC development is logged here, classified by component, with the exact fix.

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                   MAF-CLI (Docker)                   │
│            Auth · Routing · App Shell                │
│                   Port 8080/6100                     │
├──────────────────────┬──────────────────────────────┤
│   Frontend (React)   │      Backend (Node/Java)     │
│   Webpack · MFE      │      API · DB · Logic        │
│   yarn run watch     │      npm run dev / mvn       │
└──────────────────────┴──────────────────────────────┘
         ↓ symlink                    ↓ entryPoint
  ~/Documents/EMP/apps/nfc    host.docker.internal:{port}
```

Three concurrent processes. If any one breaks, the whole stack fails differently.

---

## Component Nodes

- [[MAF-CLI]] — Docker container, routing, auth, symlink discovery
- [[NFC-Frontend]] — React microfrontend, Webpack, build/watch pipeline
- [[NFC-Backend]] — Node.js or Java Spring Boot API server
- [[NFC-Registry]] — npm registry, GitLab PAT, `.npmrc` configuration
- [[NFC-Environment]] — NVM, Node.js, Yarn, Nodemon, tool installation
- [[NFC-SSL]] — Self-signed certificates, browser exceptions

---

## Error Log

> One file per error. Add new entries as you encounter them.

### Environment & Setup
- [[Homebrew Node Breaks Docker Mounts]] — `maf-cli start` fails with Docker mount error
- [[NVM Not Found After Install]] — `command not found: nvm` after curl install
- [[Husky Git Not Found on Yarn Install]] — Husky can't find `.git` from frontend subdirectory

### Registry & Auth
- [[npm 404 on Nielsen Packages]] — `.npmrc` misconfigured or PAT invalid
- [[GitLab PAT Expired Docker Fails]] — `HTTP Basic: Access denied` on Docker commands
- [[npmrc Token Committed to Git]] — credentials silently exposed in repo history

### MAF-CLI & Docker
- [[Port Already In Use EADDRINUSE]] — port 6100/8080 occupied by leftover process
- [[File Directory Listing Instead of UI]] — symlink missing or pointing to wrong path
- [[Blank Page After Login SSL Rejection]] — self-signed cert blocking microfrontend assets

### Frontend
- [[Webpack Watch Not Updating]] — save file but browser doesn't change
- [[Symlink Points to Wrong Build]] — app shows old version after project move

### Backend (Setup)
- [[JAVA_HOME Not Set Spring Boot]] — `mvn spring-boot:run` can't find Java 21
- [[Seed.js EntryPoint Mismatch]] — API calls fail because MAF can't reach localhost from Docker

### Backend (Runtime)
- [[Duplicate Key Processing Queue Kafka]] — Kafka consumer reprocesses event, hits unique constraint on `processing_queue`

---

## Quick Diagnostic

```
NFC not loading?
│
├── Docker Desktop running?       → No → Start Docker Desktop
├── All 3 terminals running?      → No → Start missing process
├── Symlink exists?               → No → see [[File Directory Listing Instead of UI]]
├── Page blank?                   → see [[Blank Page After Login SSL Rejection]]
├── File directory listing?       → see [[Symlink Points to Wrong Build]]
├── API calls failing?            → see [[Seed.js EntryPoint Mismatch]]
└── Port conflict?                → see [[Port Already In Use EADDRINUSE]]
```

---

## Quick Reference

```bash
# Start full stack (3 terminals)
cd ~/Documents/code/nfc/backend && npm run dev
cd ~/Documents/code/nfc/frontend && yarn run watch:{env}
cd ~/Documents/code/nfc/frontend/conf/dev-env && maf-cli start

# Rebuild frontend
cd ~/Documents/code/nfc/frontend && yarn build

# Verify symlink
ls -la ~/Documents/EMP/apps

# Kill port conflicts
kill -9 $(lsof -ti:3001)
kill -9 $(lsof -ti:6100)
kill -9 $(lsof -ti:8080)
```

### Port Map
| Port | Service |
|---|---|
| 3001 | Backend API |
| 6100 | MAF-CLI |
| 8080 | MAF Application Shell |

## My Notes

