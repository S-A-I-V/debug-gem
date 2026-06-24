---
tags: [devops, build-tool, java, kotlin]
aliases: [gradle]
---
# Gradle

Build tool for [[Java]]/Kotlin. Groovy or Kotlin DSL. More flexible than [[Maven]], used by Android.

## Known For These Error Patterns
- [[Dependency & Import Errors]] — Dependency resolution failures, version conflicts, transitive dependency issues
- [[Configuration & Environment Errors]] — JDK version mismatch, plugin version incompatibility, Gradle/Wrapper version mismatch
- [[Resource Exhaustion Errors]] — Heap out of memory during build, Metaspace overflow, daemon memory leak
- [[Syntax & Parse Errors]] — Build script DSL errors (Groovy/Kotlin)

## Common Errors
- `Could not resolve all dependencies` — Dependency not in any configured repository. Check `repositories {}` block.
- `Execution failed for task ':compileJava'` — Compilation error. Read the actual compiler message below.
- `java.lang.OutOfMemoryError: Java heap space` — Increase daemon memory: `org.gradle.jvmargs=-Xmx4g` in `gradle.properties`
- `Incompatible because this component was published for Java X` — Project targets different JDK. Set `sourceCompatibility`/`targetCompatibility`.
- `Plugin [id: 'X'] was not found` — Plugin not in `pluginManagement`, or repository missing
- `Could not determine the dependencies of task` — Circular task dependency or misconfigured task inputs/outputs

## Common Gotchas
- Gradle daemon persists between builds — can accumulate memory. Kill with `gradle --stop`
- Use `./gradlew` (Gradle Wrapper) for consistent versions across team
- Build cache can mask real errors — `./gradlew clean build` to verify
- `implementation` vs `api` — `api` exposes dependency to consumers, `implementation` doesn't. Use `implementation` by default.
- Configuration phase vs execution phase — expensive operations in configuration block run EVERY build (even `gradle help`)
- Kotlin DSL has better IDE support but worse documentation and slower script compilation

## Key Commands
- `./gradlew dependencies` — full dependency tree
- `./gradlew dependencyInsight --dependency <name>` — why is this dep included?
- `./gradlew build --scan` — build scan with detailed performance info
- `./gradlew --refresh-dependencies` — force re-download dependencies

## Related
- [[Maven]] — alternative build tool
- [[Java]] — primary language
- [[MOC — DevOps & CI-CD]]

## My Notes

