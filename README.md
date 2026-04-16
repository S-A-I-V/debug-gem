# 🔮 Rosetta — Universal Debugging Knowledge Base

> A compressed knowledge system that encodes the taxonomy of software failure. Not a list of errors, but the patterns behind every error ever.

## What Is This?

**83 interconnected markdown files** encoding how software fails across every domain. Designed for:

1. **Gemini Gem** — Upload as knowledge files for an AI debugging assistant
2. **Obsidian Graph** — Visual error pattern exploration with clickable nodes

## Repository Structure

```
debug-gem/
├── Master_Logic.md              ← The brain. 12 universal failure patterns + decision tree.
├── Gem_Instructions.md          ← System prompt for your Gemini Gem.
├── README.md                    ← debug-gem subfolder readme.
│
├── MOC — Languages & Runtimes.md    ← Python, JS, TS, Java, Go, Rust, C++, Ruby, PHP, Swift, C#
├── MOC — Cloud & Infrastructure.md  ← AWS, GCP, Azure, Docker, Kubernetes, Terraform, Linux
├── MOC — AI & ML.md                 ← CUDA, PyTorch, TensorFlow, LLMs, HuggingFace, Pandas, Spark
├── MOC — Blockchain & Web3.md       ← Solidity, Ethereum, DeFi, NFTs, Ethers.js
├── MOC — Databases & Data.md        ← PostgreSQL, MySQL, MongoDB, Redis, Elasticsearch, Kafka, DynamoDB
├── MOC — Networking & APIs.md       ← HTTP status codes, REST, GraphQL, gRPC, WebSocket, DNS, TLS
├── MOC — Security & Auth.md         ← OAuth, JWT, SAML, RBAC, XSS, SQLi, CORS, CSP
├── MOC — Frontend & UI.md           ← React, Next.js, Vue, Angular, CSS, React Native, Flutter
├── MOC — DevOps & CI-CD.md          ← Git, GitHub Actions, GitLab CI, npm, Maven, deployment patterns
│
└── nodes/                           ← 72 clickable technology & concept pages
    ├── Python.md, JavaScript.md, TypeScript.md, Java.md, Go.md, Rust.md, ...
    ├── AWS.md, Docker.md, Kubernetes.md, Terraform.md, Linux.md, ...
    ├── PostgreSQL.md, Redis.md, MongoDB.md, Kafka.md, ...
    ├── React.md, Next.js.md, Vue.md, Angular.md, ...
    ├── Solidity.md, Ethereum.md, ...
    ├── The Boundary Problem.md, The Silent Failure.md, ...
    └── ... (every [[wikilinked]] concept has its own page)
```

## Setup: Gemini Gem

1. Go to gemini.google.com/gems
2. Create a new Gem named "Rosetta"
3. Paste contents of `debug-gem/Gem_Instructions.md` into the Instructions field
4. Upload `debug-gem/Master_Logic.md` and all `MOC — *.md` files as Knowledge
5. Test with: "I'm getting a CUDA out of memory error when training my model"

## Setup: Obsidian

1. Create a new Obsidian vault (or use existing)
2. Copy the `debug-gem/` folder contents into the vault root
3. Open Graph View (Cmd + G)
4. The `[[wikilinks]]` auto-connect into a knowledge graph
5. Click any node to see its description, error patterns, and your notes

### Making Your Graph Look Amazing

**Color by tags** (in Graph View settings):
- `tag:#language` → Blue (Python, JS, Java, Go, Rust, etc.)
- `tag:#database` → Green (PostgreSQL, Redis, MongoDB, etc.)
- `tag:#cloud` → Orange (AWS, GCP, Azure)
- `tag:#security` → Red (OAuth, JWT, SAML)
- `tag:#ai` → Purple (CUDA, PyTorch, LLM)
- `tag:#frontend` → Yellow (React, Vue, Angular)
- `tag:#devops` → Cyan (Git, Docker, Kubernetes)
- `tag:#meta-pattern` → White/bright (The Boundary Problem, etc.)

**Graph settings for best visual**:
- Forces → Repel force: 10-15 (spreads nodes out)
- Forces → Link force: 0.5-1.0 (keeps connected nodes close)
- Display → Node size: scale by number of links
- Display → Show tags: off (cleaner look)
- Filters → Show orphans: off (hides unlinked files)

**Pro tips**:
- More bidirectional links = tighter clusters. Each node page links BACK to its MOC and patterns.
- Tags create invisible grouping. Color by tag to see domain clusters emerge.
- The "hub" nodes (Master_Logic, MOC files, universal patterns) will naturally be the largest.
- Zoom into a cluster and screenshot it — that's your hackathon demo.

## Extending the Knowledge Base

### Add a new technology
1. Create `debug-gem/nodes/NewTech.md` with the template:
```markdown
---
tags: [relevant, tags, here]
aliases: [aliases]
---
# NewTech

One-line description.

## Known For These Error Patterns
- [[Pattern Name]] — specific error description

## Common Gotchas
- Gotcha 1
- Gotcha 2

## Related
- [[Related Tech]]
- [[Relevant MOC]]

## My Notes

```

2. Add `[[NewTech]]` references in the relevant MOC file
3. The graph updates automatically

### Add your own debugging notes
Every node page has a `## My Notes` section at the bottom. Write your personal experiences, solutions, and context there. This is YOUR knowledge base — the templates are just the starting point.

## Stats

<div align="center">

![Files](https://img.shields.io/badge/Markdown_Files-83-blueviolet?style=for-the-badge&logo=obsidian&logoColor=white)
![Lines](https://img.shields.io/badge/Lines_of_Knowledge-4500+-blue?style=for-the-badge&logo=bookstack&logoColor=white)
![Size](https://img.shields.io/badge/Total_Size-~500KB-lightgrey?style=for-the-badge&logo=files&logoColor=white)

| Category | Count | Coverage |
|---|---|---|
| Maps of Content (MOCs) | 9 | All major domains |
| Technology Node Pages | 72 | Languages · Cloud · DB · Frontend · Security · AI · Web3 · DevOps |
| Universal Failure Patterns | 12 | Every error type ever |
| Meta-Patterns | 6 | Cross-cutting failure archetypes |

```
Domain Coverage
─────────────────────────────────────────────────────
Languages & Runtimes   ████████████████████  11 nodes
Cloud & Infra          ████████████████      8 nodes
Databases & Data       ████████████████      8 nodes
Frontend & UI          ████████████          6 nodes
DevOps & CI/CD         ████████████          6 nodes
AI & ML                ████████              5 nodes
Networking & APIs      ████████              5 nodes
Security & Auth        ████████              5 nodes
Blockchain & Web3      ████████              4 nodes
─────────────────────────────────────────────────────
```

</div>
