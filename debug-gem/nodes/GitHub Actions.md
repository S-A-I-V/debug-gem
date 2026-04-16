---
tags: [devops, ci-cd, automation, github]
aliases: [github-actions, gha]
---
# GitHub Actions

CI/CD platform built into GitHub. Workflow files in `.github/workflows/`. YAML-based.

## Known For These Error Patterns
- [[Permission & Authorization Errors]] — `GITHUB_TOKEN` scope too narrow, `Resource not accessible by integration`
- [[Resource Exhaustion Errors]] — Disk full (14GB limit), timeout, API rate limit
- [[Configuration & Environment Errors]] — Missing inputs, wrong action version, YAML syntax errors
- [[Dependency & Import Errors]] — Action not found, command not found (exit code 127)

## Common Gotchas
- Secrets NOT available in fork PRs (security measure)
- `GITHUB_TOKEN` is repo-scoped — use PAT for cross-repo access
- `actions/checkout` defaults to shallow clone (`fetch-depth: 1`)
- Matrix job failure cancels all by default — use `fail-fast: false`

## Related
- [[GitLab CI]] — alternative CI/CD
- [[Git]] — version control
- [[MOC — DevOps & CI-CD]]

## My Notes

