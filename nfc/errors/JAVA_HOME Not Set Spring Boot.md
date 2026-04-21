---
tags: [nfc, error, backend, java]
date: 2026-04-20
severity: 🟡 Medium
status: resolved
---
# JAVA_HOME Not Set Spring Boot

## Error
```
JAVA_HOME is not set
```
or `mvn spring-boot:run` fails to find Java 21.

## Root Cause
Java 21 not installed, or `JAVA_HOME` environment variable not pointing to it.

## Fix
```bash
export JAVA_HOME=$(/usr/libexec/java_home -v 21 2>/dev/null || echo "/opt/homebrew/opt/openjdk@21/libexec/openjdk.jdk/Contents/Home")
export PATH="$JAVA_HOME/bin:$PATH"

# Verify
java -version   # Should show 21.x
```

Add to `~/.zshrc` to persist across sessions.

## Time to Fix
~2 min.

## Connected To
- [[NFC-Backend]]
