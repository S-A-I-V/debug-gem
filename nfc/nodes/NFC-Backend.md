---
tags: [nfc, backend, nodejs, java, spring-boot]
aliases: [nfc-backend, nfc-api]
---
# NFC-Backend

API server supporting two runtimes: Node.js (legacy) with Nodemon, or Java Spring Boot (current).

## Errors Logged
- [[JAVA_HOME Not Set Spring Boot]]
- [[Seed.js EntryPoint Mismatch]]
- [[Duplicate Key Processing Queue Kafka]] — Kafka consumer reprocesses event, hits unique constraint on `processing_queue`

## Key Facts
- `seed.js` must use `host.docker.internal:{port}`, not `localhost` (MAF runs in Docker)
- Java 21 required for Spring Boot
- Node backend uses Nodemon for auto-restart

## Startup
```bash
# Node.js
cd backend && npm run dev

# Java Spring Boot
export JAVA_HOME=$(/usr/libexec/java_home -v 21)
export PATH="$JAVA_HOME/bin:$PATH"
SERVER_PORT={port} SERVER_SERVLET_CONTEXT_PATH=/nfc mvn spring-boot:run
```

## Related
- [[MAF-CLI]] — routes API requests to backend
- [[NFC-Frontend]] — consumes backend API
- [[NFC]]

## My Notes

