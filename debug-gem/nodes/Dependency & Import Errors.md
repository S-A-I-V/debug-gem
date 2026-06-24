---
tags: [error-pattern, universal-failure-pattern]
aliases: [dependency-errors, import-errors, module-not-found, package-errors]
---
# Dependency & Import Errors

> Can't find or load what you need. A required module, package, library, or service is missing, incompatible, or can't be resolved.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. The code references something external that can't be found, loaded, or used in its current form.

## The Pattern
Code depends on external packages, modules, or services. This pattern fires when:
- Package/module doesn't exist or can't be found
- Version conflict between dependencies
- Import path is wrong
- Binary/native dependency missing
- Circular dependency detected

## Universal Symptoms
- `ModuleNotFoundError`, `MODULE_NOT_FOUND`, `ImportError`
- `NoClassDefFoundError`, `ClassNotFoundException`
- `Cannot find module 'X'`
- `Package 'X' not found`, `No matching version`
- `ERESOLVE`, `peer dep conflicts`
- `undefined symbol`, `symbol not found` (linker errors)
- `ImagePullBackOff` (Kubernetes)
- Exit code 127 (command not found)

## Diagnostic Decision Tree
1. **DOES IT EXIST?** Is the package name correct? Is it published?
2. **IS IT INSTALLED?** Check node_modules, site-packages, classpath
3. **IS THE VERSION RIGHT?** Version conflict? Peer dep mismatch?
4. **IS THE PATH RIGHT?** Relative vs absolute import, case sensitivity
5. **IS IT THE RIGHT FORMAT?** ESM vs CJS, Python 2 vs 3
6. **IS IT A NATIVE DEP?** Missing system library, wrong architecture

## Common Causes

### Not Installed
- Forgot to run `npm install` / `pip install` / `bundle install`
- Package not in `package.json` / `requirements.txt`
- Installing in wrong environment (global vs local, wrong virtualenv)
- Lock file out of sync with manifest

### Version Conflicts
- Two packages need different versions of same dependency
- Peer dependency version mismatch
- Breaking change in major version update
- Lock file conflict after merge

### Wrong Path/Name
- Typo in import path
- Case sensitivity (macOS is case-insensitive, Linux is not)
- Relative import `./` missing
- ESM vs CommonJS import syntax mismatch

### Missing Native/System Dependencies
- C extension needs compiler (`gcc`, `python3-dev`)
- Binary not available for architecture (ARM vs x86)
- System library missing (`libssl-dev`, `libc`)
- Alpine Linux missing `glibc` (uses `musl`)

### Circular Dependencies
- Module A imports B, B imports A
- Usually a design problem — extract shared code to third module

## Fix Strategies
1. **Verify installation** — check package is actually installed in correct environment
2. **Clean and reinstall** — delete lock file + node_modules/venv, reinstall fresh
3. **Pin versions** — use exact versions in lock files
4. **Resolve conflicts** — use resolutions/overrides to force compatible versions
5. **Check platform** — ensure package supports your OS/architecture
6. **Fix circular deps** — restructure code, extract shared module

## Technologies That Commonly Produce This
- [[npm]], [[yarn]], [[pnpm]] — JavaScript package resolution
- [[Python]] — virtualenv, pip conflicts, circular imports
- [[Java]], [[Maven]], [[Gradle]] — classpath hell, version conflicts
- [[TypeScript]] — missing @types packages, declaration file issues
- [[Go]] — module resolution, go.sum mismatches
- [[Rust]] — Cargo feature flags, linking errors
- [[Ruby]] — Gem conflicts, Bundler issues
- [[Docker]] — missing system libraries in slim images
- [[Kubernetes]] — ImagePullBackOff
- [[C++]] — linker errors, missing headers
- [[React Native]] — native module conflicts

## Related Patterns
- [[Configuration & Environment Errors]] — wrong env can cause packages not found
- [[Permission & Authorization Errors]] — registry auth failure looks like not-found
- [[The Environment Delta]] — "works on my machine" = different installed packages

## My Notes

