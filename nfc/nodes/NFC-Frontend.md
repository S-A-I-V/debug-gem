---
tags: [nfc, frontend, react, webpack]
aliases: [nfc-frontend, nfc-ui]
---
# NFC-Frontend

React microfrontend built with Webpack. Served through MAF-CLI via symlink at `~/Documents/EMP/apps/nfc`.

## Errors Logged
- [[Webpack Watch Not Updating]]
- [[Symlink Points to Wrong Build]]
- [[Husky Git Not Found on Yarn Install]]

## Key Commands
```bash
yarn install --ignore-scripts    # always use --ignore-scripts
yarn build                       # build for MAF-CLI
yarn run watch:{env}             # hot-reload mode
```

## Related
- [[MAF-CLI]] — serves the built frontend
- [[NFC-Backend]] — API that frontend calls
- [[NFC-SSL]] — browser cert exceptions for asset loading
- [[NFC]]

## My Notes

