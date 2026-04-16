---
tags: [devops, version-control, tool]
aliases: [git]
---
# Git

Distributed version control system. Foundation of modern software development.

## Known For These Error Patterns
- [[Concurrency & Race Condition Errors]] — Merge conflicts, push rejected (non-fast-forward)
- [[State & Lifecycle Errors]] — Detached HEAD, unrelated histories, branch naming conflicts
- [[Permission & Authorization Errors]] — Auth failures, protected branch rules
- [[Configuration & Environment Errors]] — Remote URL wrong, gitignore not working

## Essential Commands
- `git log --oneline --graph --all` — visual history
- `git bisect` — binary search for bug-introducing commit
- `git reflog` — recover "lost" commits
- `git stash` — temporarily shelve changes
- `git blame` — who changed each line

## Common Gotchas
- `.gitignore` only affects untracked files — use `git rm --cached` for already-tracked
- Secrets in history are permanent — rotate immediately, use BFG to clean
- `--force` overwrites remote — use `--force-with-lease` instead

## Related
- [[GitHub Actions]], [[GitLab CI]] — CI/CD platforms
- [[MOC — DevOps & CI-CD]]

## My Notes

