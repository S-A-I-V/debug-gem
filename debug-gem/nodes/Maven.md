---
tags: [devops, build-tool, java]
aliases: [maven, mvn]
---
# Maven

Build tool and dependency manager for [[Java]]. Uses `pom.xml`. Central repository at search.maven.org. Convention over configuration.

## Known For These Error Patterns
- [[Dependency & Import Errors]] — `Could not find artifact`, version conflicts, transitive dependency hell
- [[Configuration & Environment Errors]] — Wrong JDK version, plugin configuration errors, profile not activated
- [[Resource Exhaustion Errors]] — OOM during build, download timeouts
- [[Permission & Authorization Errors]] — Repository authentication failures, deploy permissions

## Common Errors
- `Could not find artifact X in central` — Package doesn't exist or is in a different repository. Check group/artifact/version.
- `NoSuchMethodError` / `NoClassDefFoundError` at runtime — Dependency version conflict. Two libraries need different versions of same dependency.
- `Non-resolvable parent POM` — Parent POM not available. Check repository configuration and parent version.
- `Plugin execution not covered by lifecycle configuration` — Eclipse-specific. Add plugin management or lifecycle mapping.
- `Fatal error compiling: invalid target release: X` — Maven compiling with wrong JDK. Set `maven.compiler.source` and `maven.compiler.target`.
- `BUILD FAILURE` with `Return code is: 409` — Trying to overwrite released (non-SNAPSHOT) artifact.

## Key Commands
- `mvn dependency:tree` — full dependency tree (find conflicts)
- `mvn dependency:analyze` — find unused and undeclared dependencies
- `mvn help:effective-pom` — see final resolved POM (after inheritance/interpolation)
- `mvn clean install -U` — force update snapshots and rebuild
- `mvn versions:display-dependency-updates` — check for newer versions

## Common Gotchas
- Local `.m2` cache corruption — delete `~/.m2/repository/<group>/<artifact>` and rebuild
- SNAPSHOT versions can change without version bump — never use in production builds. Pin exact versions.
- Use `./mvnw` (Maven Wrapper) for consistent versions across team
- Dependency mediation — Maven picks "nearest" version in tree, not newest. Use `<dependencyManagement>` to force versions.
- `provided` scope means available at compile but NOT at runtime — wrong scope causes runtime ClassNotFoundException
- Profile activation — `-P <profile>` on command line, or `activeByDefault` (which deactivates when any other profile is active!)

## Debug Dependency Conflicts
1. `mvn dependency:tree -Dverbose` — show all versions including conflicts
2. Find the conflicting dependency
3. Add `<exclusion>` to remove unwanted version
4. Or pin version in `<dependencyManagement>` section
5. Verify: `mvn dependency:tree | grep <artifact>`

## Related
- [[Gradle]] — alternative build tool (more flexible, less convention)
- [[Java]] — primary language
- [[MOC — DevOps & CI-CD]]

## My Notes

