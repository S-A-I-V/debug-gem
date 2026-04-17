# Security Policy

## Scope

0xDEBUG is a static markdown knowledge base. It contains no executable code, no backend services, no user data collection, and no dependencies. The attack surface is effectively zero.

That said, the following are still valid security concerns for this repo:

- Malicious content embedded in markdown (e.g. XSS via rendered HTML in certain viewers)
- Misinformation in security-related nodes (OAuth, JWT, SAML, CORS, etc.) that could mislead developers
- Sensitive data accidentally committed (API keys, credentials in examples)

## Reporting a Vulnerability

If you find any of the above — or anything else that feels wrong — please **do not open a public issue.**

Report privately via:
- GitHub: Use the [Private Security Advisory](../../security/advisories/new) feature
- Email: Open an issue marked `[SECURITY]` if the above is unavailable

**What to include:**
1. Description of the concern
2. Which file(s) are affected
3. Potential impact

## Response Timeline

| Severity | Response Target |
|----------|----------------|
| Critical (e.g. credentials in history) | 24 hours |
| Medium (misleading security content) | 72 hours |
| Low (minor inaccuracies) | Next release cycle |

## Content Accuracy

Security-related nodes (JWT, OAuth, SAML, CORS, CSP, XSS, SQLi) are maintained for educational accuracy. If you spot outdated or incorrect security guidance, opening a regular issue or PR is encouraged.
