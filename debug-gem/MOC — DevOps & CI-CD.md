# MOC — DevOps & CI-CD

> Error patterns for build systems, CI/CD pipelines, version control, deployment, and monitoring. This is where [[The Environment Delta]] and [[Configuration & Environment Errors]] are most prevalent.

---

## [[Git]]

### Common Errors
- `CONFLICT (content): Merge conflict in X` → [[Concurrency & Race Condition Errors]]: Two branches modified same lines.
  - **Fix**: Open conflicted files, resolve `<<<<<<<`/`=======`/`>>>>>>>` markers, `git add`, `git commit`. Test after resolving.
- `error: Your local changes to the following files would be overwritten by merge` → [[State & Lifecycle Errors]]: Uncommitted changes conflict with incoming changes.
  - **Fix**: `git stash`, then merge/pull, then `git stash pop`.
- `fatal: refusing to merge unrelated histories` → [[State & Lifecycle Errors]]: Repos have no common ancestor.
  - **Fix**: Use `--allow-unrelated-histories` if intentional (e.g., combining two repos).
- `error: failed to push some refs to 'X'` → [[Concurrency & Race Condition Errors]]: Remote has commits you don't have.
  - **Fix**: `git pull --rebase` then `git push`. Or `git pull` then `git push`.
- `fatal: remote origin already exists` → [[Configuration & Environment Errors]]: Remote already configured.
  - **Fix**: `git remote set-url origin <new-url>` to change, or `git remote remove origin` then `git remote add`.
- `HEAD detached at X` → [[State & Lifecycle Errors]]: Checked out a commit, not a branch.
  - **Fix**: `git checkout -b <new-branch>` to create branch from current position, or `git checkout <branch>` to return.
- `fatal: not a git repository` → [[Configuration & Environment Errors]]: Not in a git repo. Run `git init` or check directory.
- `error: pathspec 'X' did not match any file(s) known to git` → [[Configuration & Environment Errors]]: File doesn't exist or isn't tracked.
- `fatal: bad object X` → [[State & Lifecycle Errors]]: Corrupted git object. May need to re-clone.
- `error: cannot lock ref 'refs/heads/X': 'refs/heads/X/Y' exists` → [[State & Lifecycle Errors]]: Branch naming conflict (branch `X` and `X/Y` can't coexist).
- `warning: LF will be replaced by CRLF` → [[Serialization & Encoding Errors]]: Line ending conversion. Configure `core.autocrlf`.
- `error: RPC failed; HTTP 413 curl 22 The requested URL returned error: 413` → [[Resource Exhaustion Errors]]: Push too large. Increase `http.postBuffer`.
- `remote: error: GH006: Protected branch update failed` → [[Permission & Authorization Errors]]: Branch protection rules preventing push.
- `remote: Permission to X denied to Y` → [[Permission & Authorization Errors]]: Wrong credentials or no access.
- `fatal: Authentication failed` → [[Permission & Authorization Errors]]: Wrong username/password/token. GitHub deprecated password auth — use PAT or SSH.
- `error: insufficient permission for adding an object to repository database` → [[Permission & Authorization Errors]]: File system permissions on .git directory.

### Git Traps
- **`.gitignore` not ignoring tracked files**: `.gitignore` only affects untracked files. For already-tracked files: `git rm --cached <file>`.
- **Large files in history**: Even after deleting, large files remain in git history. Use `git-lfs` for large files. Use BFG Repo-Cleaner or `git-filter-repo` to remove from history.
- **Sensitive data committed**: Secrets in git history are permanent (even after deletion). Rotate the secret immediately. Use BFG to remove from history. Use `git-secrets` or pre-commit hooks to prevent.
- **Force push dangers**: `git push --force` overwrites remote history. Use `--force-with-lease` instead (fails if remote changed).
- **Rebase vs merge**: Rebase rewrites history (clean but dangerous on shared branches). Merge preserves history (safe but noisy).
- **Submodule pain**: Submodules are confusing. `git submodule update --init --recursive` after clone. Consider alternatives (monorepo, package manager).
- **Detached HEAD commits**: Commits in detached HEAD are orphaned if you switch branches without creating a branch. They'll be garbage collected.

### Git Debug
- `git log --oneline --graph --all` — visual branch history.
- `git bisect` — binary search for the commit that introduced a bug.
- `git blame <file>` — who changed each line and when.
- `git reflog` — history of HEAD movements. Recover "lost" commits.
- `git diff --staged` — see what's staged for commit.
- `git stash list` — see stashed changes.
- `git remote -v` — see remote URLs.
- `git config --list --show-origin` — see all config and where it's set.

---

## [[GitHub Actions]]

### Common Errors
- `Error: Resource not accessible by integration` → [[Permission & Authorization Errors]]: `GITHUB_TOKEN` lacks required permissions.
  - **Fix**: Add `permissions:` block to workflow. Common: `contents: write`, `pull-requests: write`, `packages: write`.
- `Error: Process completed with exit code 1` → Generic failure. Check the step's output for the real error.
- `Error: Process completed with exit code 2` → Command not found or misuse of shell command.
- `Error: Process completed with exit code 127` → [[Dependency & Import Errors]]: Command not found. Install the tool first.
- `Error: Process completed with exit code 128` → Git error (often authentication or permission).
- `Error: No space left on device` → [[Resource Exhaustion Errors]]: Runner disk full (14GB limit on GitHub-hosted runners).
  - **Fix**: Clean caches, use smaller Docker images, free space with `docker system prune`.
- `Error: The operation was canceled` → [[State & Lifecycle Errors]]: Workflow cancelled (manually, by concurrency group, or timeout).
- `Error: Timeout` → [[Resource Exhaustion Errors]]: Step exceeded timeout (default 360 minutes for job).
- `Error: Container action is only supported on Linux` → [[Configuration & Environment Errors]]: Docker actions only work on Linux runners.
- `Error: Input required and not supplied: X` → [[Configuration & Environment Errors]]: Action input missing.
- `Error: Unable to resolve action 'X@Y'` → [[Dependency & Import Errors]]: Action doesn't exist or version tag wrong.
- `Error: HttpError: API rate limit exceeded` → [[Resource Exhaustion Errors]]: GitHub API rate limit.
- `Error: HttpError: Resource not accessible by personal access token` → [[Permission & Authorization Errors]]: PAT lacks required scope.
- `Error: The template is not valid: X` → [[Syntax & Parse Errors]]: YAML syntax error in workflow file.
- `Error: every step must define a 'uses' or 'run' key` → [[Syntax & Parse Errors]]: Step missing required field.

### GitHub Actions Traps
- **Secrets in fork PRs**: Secrets are NOT available in workflows triggered by fork PRs (security measure). Use `pull_request_target` carefully.
- **`GITHUB_TOKEN` scope**: Limited to the repository. Can't access other repos. Use PAT for cross-repo access.
- **Matrix strategy failures**: By default, one matrix job failure cancels all others. Use `fail-fast: false` to continue.
- **Cache key collisions**: Cache keys must be unique. Include OS, dependency lock file hash, and any other varying factors.
- **Concurrency groups**: Without concurrency control, multiple workflow runs can conflict. Use `concurrency:` to cancel or queue.
- **`actions/checkout` depth**: Default is shallow clone (`fetch-depth: 1`). Use `fetch-depth: 0` for full history (needed for `git log`, `git bisect`, changelog generation).
- **Environment variables vs secrets**: Env vars are visible in logs. Secrets are masked. Never put secrets in env vars directly.
- **Workflow dispatch inputs**: `workflow_dispatch` inputs are always strings. Parse numbers/booleans explicitly.
- **Composite actions**: Can't use `secrets` context directly. Pass secrets as inputs.
- **Reusable workflows**: Can't be nested more than 4 levels deep. Have different context access than regular workflows.

---

## [[GitLab CI]]

### Common Errors
- `Job failed: exit code 1` → Generic failure. Check script output.
- `Job failed (system failure)` → [[Resource Exhaustion Errors]]: Runner issue. Retry.
- `This job is stuck because the project doesn't have any runners online` → [[Configuration & Environment Errors]]: No runners matching tags.
- `Artifact too large` → [[Resource Exhaustion Errors]]: Reduce artifact size or increase limit.
- `yaml invalid` → [[Syntax & Parse Errors]]: `.gitlab-ci.yml` syntax error. Use CI Lint tool.
- `Pipeline filtered out by workflow rules` → [[Configuration & Environment Errors]]: Workflow rules excluded this pipeline.
- `Job failed: exit code 137` → [[Resource Exhaustion Errors]]: OOM killed.

### GitLab CI Traps
- **`only`/`except` vs `rules`**: Don't mix old (`only`/`except`) and new (`rules`) syntax. Use `rules` exclusively.
- **Cache vs artifacts**: Cache is for speeding up jobs (dependencies). Artifacts are for passing data between jobs/stages.
- **`needs` keyword**: Allows DAG-style pipeline (jobs run as soon as dependencies complete, not waiting for whole stage).
- **Protected variables**: Variables marked as protected are only available in protected branches/tags.

---

## Build Systems

### [[npm]] / [[yarn]] / [[pnpm]]
- `npm ERR! ERESOLVE unable to resolve dependency tree` → [[Dependency & Import Errors]]: Peer dependency conflict.
  - **Fix**: `npm install --legacy-peer-deps` (temporary), or fix version conflicts in package.json.
- `npm ERR! peer dep missing: X, required by Y` → [[Dependency & Import Errors]]: Peer dependency not installed.
- `npm ERR! 404 Not Found - GET https://registry.npmjs.org/X` → [[Dependency & Import Errors]]: Package doesn't exist or is unpublished.
- `npm ERR! code EINTEGRITY` → [[Serialization & Encoding Errors]]: Package integrity check failed. Delete `node_modules` and `package-lock.json`, reinstall.
- `npm ERR! code EACCES` → [[Permission & Authorization Errors]]: Permission denied. Don't use `sudo npm install -g`. Fix npm permissions.
- `npm ERR! code ENOENT` → [[Configuration & Environment Errors]]: File not found. Check paths in package.json scripts.
- `npm ERR! code ELIFECYCLE` → Script failed. Check the script output for the actual error.
- `npm WARN deprecated X` → Not an error but indicates package is deprecated. Find alternative.
- `FATAL ERROR: Allocation failed - JavaScript heap out of memory` → [[Resource Exhaustion Errors]]: `NODE_OPTIONS=--max-old-space-size=4096`.
- `Error: Cannot find module 'X'` → [[Dependency & Import Errors]]: Module not installed. Run `npm install`.
- `SyntaxError: Cannot use import statement outside a module` → [[Dependency & Import Errors]]: ESM/CJS mismatch. Check `type` field in package.json.

### npm/yarn/pnpm Traps
- **`package-lock.json` vs `yarn.lock`**: Don't have both. Choose one package manager.
- **`npm install` vs `npm ci`**: `npm ci` uses lock file exactly (for CI). `npm install` may update lock file.
- **Phantom dependencies**: In npm/yarn (hoisted), you can import packages not in your package.json. pnpm prevents this.
- **`postinstall` scripts**: Can run arbitrary code. Security risk from malicious packages. Use `--ignore-scripts` for untrusted packages.
- **`node_modules` size**: Can be enormous. Use `.dockerignore` to exclude. Use multi-stage Docker builds.
- **Workspace hoisting**: In monorepos, dependencies are hoisted to root. Can cause version confusion.

### [[Maven]] / [[Gradle]]
- `Could not resolve dependencies` → [[Dependency & Import Errors]]: Repository unreachable or artifact not found.
- `Compilation failure` → [[Syntax & Parse Errors]] or [[Type & Casting Errors]]: Check compiler output.
- `Could not find artifact X` → [[Dependency & Import Errors]]: Artifact not in configured repositories.
- `Non-resolvable parent POM` → [[Dependency & Import Errors]]: Parent POM not found.
- `Plugin X or one of its dependencies could not be resolved` → [[Dependency & Import Errors]]: Plugin not found.
- `java.lang.OutOfMemoryError: Java heap space` (during build) → [[Resource Exhaustion Errors]]: Increase Maven/Gradle memory.

### Maven/Gradle Traps
- **Local `.m2` cache corruption**: Delete `~/.m2/repository/<group>/<artifact>` and rebuild.
- **Gradle daemon memory**: Gradle daemon persists between builds. Can accumulate memory. `gradle --stop` to kill.
- **Multi-module build ordering**: Modules build in dependency order. Circular dependencies cause build failure.
- **SNAPSHOT versions**: SNAPSHOT versions can change without version bump. Pin versions in production.
- **Gradle vs Maven wrapper**: Use `./gradlew` and `./mvnw` to ensure consistent build tool version.

---

## Deployment Patterns & Errors

### Rolling Deployment
- **New version crashes** → [[State & Lifecycle Errors]]: Health check failing. Check readiness probe, startup time, dependencies.
- **Mixed version traffic** → [[Concurrency & Race Condition Errors]]: Old and new versions serving simultaneously.
  - **Fix**: Ensure backward compatibility. Database migrations must be compatible with both versions.
- **Rollback needed** → Revert to previous version. Ensure rollback is tested and fast.

### Blue-Green Deployment
- **Database schema mismatch** → [[State & Lifecycle Errors]]: New schema not compatible with old code (or vice versa).
  - **Fix**: Use expand-contract migration pattern. Make schema changes backward compatible.
- **DNS/LB switch failure** → [[Connection & Network Errors]]: Traffic not routing to new environment.
- **Resource cost** → [[Resource Exhaustion Errors]]: Running two full environments doubles cost.

### Canary Deployment
- **Canary not receiving traffic** → [[Configuration & Environment Errors]]: Traffic splitting not configured correctly.
- **Metrics not comparable** → [[The Silent Failure]]: Canary and baseline have different traffic patterns.
- **Canary auto-rollback** → Configure automatic rollback based on error rate, latency, or custom metrics.

### Database Migration
- `Migration failed` → [[State & Lifecycle Errors]]: Schema change incompatible with running code.
  - **Fix**: Deploy code first (backward compatible), then migrate, then deploy code that uses new schema.
- `Lock timeout during migration` → [[Concurrency & Race Condition Errors]]: Large table ALTER blocking queries.
  - **Fix**: Use online DDL tools (pt-online-schema-change, gh-ost for MySQL; `CREATE INDEX CONCURRENTLY` for PostgreSQL).
- `Migration partially applied` → [[State & Lifecycle Errors]]: Migration failed mid-way. Check migration state, fix manually if needed.
- **Irreversible migrations** → [[State & Lifecycle Errors]]: Data-destructive migrations can't be rolled back. Always have a rollback plan.

### Environment Promotion
- Works in staging, fails in prod → [[The Environment Delta]]:
  - Check: env vars, secrets, data volume, network topology, feature flags, third-party service endpoints, DNS, TLS certs.
  - **Fix**: Make staging as close to prod as possible. Use infrastructure-as-code. Diff configurations.

---

## Monitoring & Alerting

### Common Issues
- **Alert fatigue** → Too many non-actionable alerts. Tune thresholds, use anomaly detection, group related alerts.
- **Missing alerts** → [[The Silent Failure]]: Error happening but no alert configured.
  - **Fix**: Add coverage for all [[Universal Failure Taxonomy]] patterns. Monitor error rates, latency percentiles, resource usage.
- **Log volume too high** → [[Resource Exhaustion Errors]]: Reduce log level in prod, use sampling, structured logging.
- **Metrics cardinality explosion** → [[Resource Exhaustion Errors]]: Too many unique label combinations. Avoid high-cardinality labels (user ID, request ID).
- **Dashboard not showing data** → [[Configuration & Environment Errors]]: Wrong time range, wrong query, data source not configured.

### Observability Stack
- **Logs**: Structured logging (JSON). Centralize with ELK/Loki/CloudWatch. Include request ID for tracing.
- **Metrics**: Prometheus/Grafana, CloudWatch, Datadog. Track: error rate, latency (p50/p95/p99), throughput, saturation.
- **Traces**: Jaeger, Zipkin, X-Ray, Tempo. Distributed tracing across services.
- **Alerts**: PagerDuty, OpsGenie, Slack. Alert on symptoms (error rate, latency), not causes (CPU usage).

### SRE Concepts
- **SLI (Service Level Indicator)**: Measurable metric (e.g., request latency, error rate).
- **SLO (Service Level Objective)**: Target for SLI (e.g., 99.9% of requests < 200ms).
- **SLA (Service Level Agreement)**: Contract with consequences for missing SLO.
- **Error budget**: 100% - SLO = error budget. If budget is exhausted, freeze deployments and fix reliability.
- **Toil**: Repetitive, manual, automatable work. Reduce toil through automation.
- **Incident response**: Detect → Triage → Mitigate → Resolve → Postmortem. Blameless postmortems.

---

## Container Registries

### Common Errors
- `denied: requested access to the resource is denied` → [[Permission & Authorization Errors]]: Not logged in or no push permission.
- `manifest unknown` → [[Configuration & Environment Errors]]: Image tag doesn't exist.
- `unauthorized: authentication required` → [[Permission & Authorization Errors]]: Need `docker login`.
- `name unknown: repository name not known to registry` → [[Configuration & Environment Errors]]: Repository doesn't exist.
- `toomanyrequests: Too Many Requests` → [[Resource Exhaustion Errors]]: Docker Hub rate limit (100 pulls/6h for anonymous, 200 for free accounts).
  - **Fix**: Use authenticated pulls, mirror to private registry, or use GitHub Container Registry / ECR / GCR.

---

## Infrastructure as Code

### Common Patterns
- **State drift** → [[Concurrency & Race Condition Errors]]: Manual changes outside IaC tool. Detect with `plan`/`diff`. Reconcile with `import`.
- **Circular dependencies** → [[Dependency & Import Errors]]: Resources reference each other. Restructure or use explicit dependencies.
- **Secret management** → [[Permission & Authorization Errors]]: Secrets in IaC state/code. Use secret managers, encrypted state backends.
- **Blast radius** → [[Resource Exhaustion Errors]]: One change affects too many resources. Use modules, workspaces, accounts to limit blast radius.
- **Drift detection** → Schedule regular `plan` runs to detect drift. Alert on unexpected changes.
