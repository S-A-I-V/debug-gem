---
tags: [nfc, registry, npm, gitlab, auth]
aliases: [nfc-registry, nfc-npmrc]
---
# NFC-Registry

Authentication layer for Nielsen's private GitLab package registry. All `@nielsen-media/*` packages require `~/.npmrc` with a valid GitLab PAT.

## Errors Logged
- [[npm 404 on Nielsen Packages]]
- [[GitLab PAT Expired Docker Fails]]
- [[npmrc Token Committed to Git]]

## .npmrc Template
```
@nielsen-media:registry=https://gitlab.com/api/v4/packages/npm/
@nielsen-media:registry=https://gitlab.com/api/v4/projects/23296024/packages/npm/
//gitlab.com/api/v4/packages/npm/:_authToken=glpat-XXXXX
//gitlab.com/api/v4/projects/23296024/packages/npm/:_authToken=glpat-XXXXX
registry=https://registry.npmjs.org/
always-auth=true
```

## Related
- [[NFC-Environment]] — must be done before registry config
- [[MAF-CLI]] — first package that tests registry auth
- [[NFC]]

## My Notes

