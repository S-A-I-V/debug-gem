# Contributing to 0xDEBUG

Thanks for wanting to add to the knowledge base. Contributions are straightforward — every technology gets one node file.

## Adding a New Technology

1. Create `debug-gem/nodes/YourTech.md` using this template:

```markdown
---
tags: [relevant, tags, here]
aliases: [lowercase-alias]
---
# YourTech

One-line description of what it is and where it's used.

## Known For These Error Patterns
- [[Pattern Name]] — specific error or failure description

## Common Gotchas
- Gotcha 1
- Gotcha 2

## Related
- [[Related Technology]]
- [[Relevant MOC]]

## My Notes

```

2. Add a `[[YourTech]]` link in the relevant `MOC — *.md` file.
3. Link back to any error pattern nodes that apply (e.g. `[[Concurrency & Race Condition Errors]]`).

## Tag Reference

Use existing tags to keep the graph clusters consistent:

| Tag | Domain |
|-----|--------|
| `language` | Python, JS, Go, Rust, etc. |
| `database` | PostgreSQL, Redis, MongoDB, etc. |
| `cloud` | AWS, GCP, Azure, Docker, K8s |
| `security` | OAuth, JWT, SAML, etc. |
| `ai` | CUDA, PyTorch, LLM, etc. |
| `frontend` | React, Vue, Angular, etc. |
| `devops` | Git, CI/CD, deployment |
| `blockchain` | Solidity, Ethereum, Web3 |
| `networking` | HTTP, DNS, gRPC, WebSocket |

## Guidelines

- Keep descriptions tight — one paragraph max for the intro
- Error patterns should link to existing `[[pattern nodes]]` where possible
- The `## My Notes` section is intentionally left blank — it's for personal use
- Don't add technologies without real debugging surface area (no "Hello World" libs)

## Submitting

Open a PR with your new node file and the MOC update. That's it.
