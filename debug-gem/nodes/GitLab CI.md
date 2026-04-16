---
tags: [devops, ci-cd, automation, gitlab]
aliases: [gitlab-ci, gitlab]
---
# GitLab CI

CI/CD platform built into GitLab. `.gitlab-ci.yml` configuration. Runners execute jobs.

## Known For These Error Patterns
- [[Resource Exhaustion Errors]] — Runner not available, artifact too large, OOM (exit 137)
- [[Configuration & Environment Errors]] — Pipeline filtered by workflow rules, protected variables
- [[Syntax & Parse Errors]] — YAML invalid

## Common Gotchas
- Don't mix `only`/`except` (old) with `rules` (new) syntax
- Cache is for speed (dependencies), artifacts are for passing data between jobs
- Protected variables only available in protected branches/tags

## Related
- [[GitHub Actions]] — alternative CI/CD
- [[Git]] — version control
- [[MOC — DevOps & CI-CD]]

## My Notes

