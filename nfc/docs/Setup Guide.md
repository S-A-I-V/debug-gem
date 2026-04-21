---
tags: [nfc, documentation, setup]
aliases: [nfc-setup-guide]
---
# NFC Setup Guide — Complete Reference

> Full local development setup for the Nielsen Fulfillment Center application.

---

## 1. Pre-flight Checklist

- [ ] macOS or Linux
- [ ] Stable internet connection
- [ ] GitLab account with Developer permissions
- [ ] Docker Desktop installed
- [ ] GitLab Personal Access Token created (ALL scopes, `glpat-` prefix)

### Create GitLab PAT
1. GitLab → Profile avatar → Preferences → Access Tokens
2. Name: `nfc-{env}-environment-token`
3. Scopes: **Select ALL**
4. Copy the `glpat-` token immediately — shown only once

---

## 2. Environment Setup

### ⚠️ Do NOT Use Homebrew for Node.js
See [[Homebrew Node Breaks Docker Mounts]] for why. Homebrew paths break MAF-CLI Docker mounts.

```bash
# If Homebrew Node exists, purge it first
brew uninstall node
brew uninstall nvm
brew cleanup
```

### Install NVM
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.zshrc   # or source ~/.bashrc
nvm --version
```

### Install Node.js
```bash
nvm install --lts
nvm use --lts
nvm alias default node
node --version    # 18.x or higher
npm --version     # 9.x or higher
```

### Install Yarn & Nodemon
```bash
npm install -g yarn
npm install -g nodemon
yarn --version      # 1.22.x
nodemon --version   # 2.x
```

---

## 3. Registry & MAF-CLI Configuration

### Create ~/.npmrc
```bash
vim ~/.npmrc
```

Add:
```
@nielsen-media:registry=https://gitlab.com/api/v4/packages/npm/
@nielsen-media:registry=https://gitlab.com/api/v4/projects/23296024/packages/npm/
//gitlab.com/api/v4/packages/npm/:_authToken=<your_glpat_token>
//gitlab.com/api/v4/projects/23296024/packages/npm/:_authToken=<your_glpat_token>
registry=https://registry.npmjs.org/
always-auth=true
```

🔐 Never commit `~/.npmrc` to Git. See [[npmrc Token Committed to Git]].

### Install MAF-CLI
```bash
npm install -g @nielsen-media/maf-cli
maf-cli --version
```

If 404 error → see [[npm 404 on Nielsen Packages]].

### Verify
```bash
npm config list
npm view @nielsen-media/maf-cli
```

---

## 4. Project Setup

### Clone & Branch
```bash
cd ~/Documents/code
git clone <repository-url> nfc
cd nfc
git fetch origin
git branch -a
git switch -c <branch> origin/<branch>
```

### Install Frontend
```bash
cd frontend
yarn install --ignore-scripts    # see [[Husky Git Not Found on Yarn Install]] for why
yarn build
```

### Install Backend
```bash
cd ../backend
npm install
```

### Create Symlink (Critical)
```bash
mkdir -p ~/Documents/EMP/apps
cd ../frontend
yarn build
ln -s ~/Documents/code/nfc/frontend/build/nfc ~/Documents/EMP/apps
```

Verify: `ls -la ~/Documents/EMP/apps`
Should show: `nfc -> /Users/.../frontend/build/nfc`

If wrong → see [[File Directory Listing Instead of UI]].

---

## 5. Running the Application

### Terminal 1 — Backend
```bash
# Node.js
cd ~/Documents/code/nfc/backend && npm run dev

# Java Spring Boot
export JAVA_HOME=$(/usr/libexec/java_home -v 21 2>/dev/null || echo "/opt/homebrew/opt/openjdk@21/libexec/openjdk.jdk/Contents/Home")
export PATH="$JAVA_HOME/bin:$PATH"
SERVER_PORT={port} SERVER_SERVLET_CONTEXT_PATH=/nfc mvn spring-boot:run
```

### Terminal 2 — Frontend Watch
```bash
cd ~/Documents/code/nfc/frontend && yarn run watch:{env}
```

### Terminal 3 — MAF-CLI
```bash
cd ~/Documents/code/nfc/frontend/conf/dev-env && maf-cli start
```

If `EADDRINUSE` → see [[Port Already In Use EADDRINUSE]].

---

## 6. Accessing the Application

1. Open the localhost URL from MAF-CLI output (typically `http://localhost:8080`)
2. Login with MAF Basic role credentials
3. If blank page → accept SSL exceptions for each port. See [[Blank Page After Login SSL Rejection]].
4. Refresh after accepting all exceptions

### SSL Exception Process
DevTools (F12) → Network tab → find red `.js` requests → right-click each → Open in new tab → Advanced → Proceed to localhost (unsafe) → close tab → repeat → refresh main app.

---

## 7. Troubleshooting Quick Reference

| Problem | Error Entry |
|---|---|
| npm 404 on @nielsen-media | [[npm 404 on Nielsen Packages]] |
| Docker login fails | [[GitLab PAT Expired Docker Fails]] |
| Token in Git | [[npmrc Token Committed to Git]] |
| Docker mount error | [[Homebrew Node Breaks Docker Mounts]] |
| `command not found: nvm` | [[NVM Not Found After Install]] |
| Husky `.git` error | [[Husky Git Not Found on Yarn Install]] |
| Port already in use | [[Port Already In Use EADDRINUSE]] |
| File directory listing | [[File Directory Listing Instead of UI]] |
| Blank page after login | [[Blank Page After Login SSL Rejection]] |
| Changes not reflecting | [[Webpack Watch Not Updating]] |
| Wrong symlink | [[Symlink Points to Wrong Build]] |
| JAVA_HOME not set | [[JAVA_HOME Not Set Spring Boot]] |
| API calls failing | [[Seed.js EntryPoint Mismatch]] |

---

## 8. Reference Links

- [GitLab Access Token Setup](https://nielsen-media.gitlab.io/maf/maf-public/media-application-framework/docs/in-depth/gitlab/setup-gitlab-access-token)
- [MAF-CLI Documentation](https://nielsen-media.gitlab.io/maf/maf-public/media-application-framework/docs/in-depth/maf-cli/)
- [MAF Environment Setup](https://nielsen-media.gitlab.io/maf/maf-public/media-application-framework/docs/migration-guides/ifl1/guides/setup-maf-env-with-example-app/)
- [Local Environment Users](https://nielsen-media.gitlab.io/maf/maf-public/media-application-framework/docs/in-depth/authentication/intro/#local-environment-users-list)
- [NVM Installation](https://github.com/nvm-sh/nvm#installation-and-update)
