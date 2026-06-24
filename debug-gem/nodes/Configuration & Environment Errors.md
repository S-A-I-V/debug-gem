---
tags: [error-pattern, universal-failure-pattern]
aliases: [config-errors, environment-errors, env-vars, misconfiguration]
---
# Configuration & Environment Errors

> Right code, wrong settings. The application logic is correct but the configuration or environment is wrong.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. The code works perfectly — with the right configuration. But the environment variables, config files, feature flags, or runtime settings are wrong for this context.

## The Pattern
Every application has configuration that varies by environment (dev/staging/prod), deployment, or context. This pattern fires when:
- Environment variable missing or has wrong value
- Config file not found or has wrong values
- Secrets/credentials wrong or expired
- Settings appropriate for one environment used in another
- Runtime version mismatch between environments

## Universal Symptoms
- `ENOENT: no such file or directory` (looking for config file)
- `undefined` environment variable used as connection string
- "Works on my machine" but fails in CI/production
- `invalid_client`, `redirect_uri_mismatch` (OAuth misconfiguration)
- Wrong database, wrong API endpoint, wrong region
- `Connection refused` to localhost in production (hardcoded dev URL)
- Feature works in dev but not prod (feature flag, different config)

## Diagnostic Decision Tree
1. **WHAT is misconfigured?** Connection string, API key, feature flag, file path?
2. **WHERE is the config set?** Env var, config file, secrets manager, hardcoded?
3. **WHAT ENVIRONMENT?** Is this dev/staging/prod? Is the config for the right env?
4. **DID IT CHANGE?** Was the config recently modified, rotated, or deployed?
5. **IS IT THE FORMAT?** Trailing whitespace, quotes, wrong encoding in env var?

## Common Causes

### Missing Configuration
- Environment variable not set in new environment
- Config file not deployed or not in expected path
- Secret not provisioned in secrets manager
- `.env` file missing (not committed to git, correctly, but not created)

### Wrong Value
- Dev URL used in production
- Test API keys in production (or vice versa)
- Wrong region/zone configured
- Wrong database connection string
- Incorrect feature flag state

### Format Issues
- Trailing newline in environment variable (common with `echo "value" > .env`)
- Quotes included as part of value (`DATABASE_URL="postgres://..."` — quotes are literal)
- Boolean as string (`"true"` vs `true`)
- Number as string (port as "8080" vs 8080)

### Version/Compatibility
- Runtime version mismatch (Node 18 vs 20, Python 3.9 vs 3.12)
- Library version compiled for different platform
- Config schema changed but old format still deployed
- Docker base image updated with breaking changes

## Fix Strategies
1. **Fail fast on missing config** — validate all required config at startup, not first use
2. **Use typed config** — parse and validate config into typed objects immediately
3. **Environment parity** — make dev/staging/prod as similar as possible
4. **Config as code** — check config into version control (not secrets)
5. **Secret management** — use secrets managers, not env files in production
6. **Document requirements** — README lists all required env vars with examples

## Debug Steps
```bash
# Check environment variables
env | grep -i DATABASE
echo $DATABASE_URL | cat -A   # show invisible characters

# Compare environments
diff <(ssh prod 'env | sort') <(ssh staging 'env | sort')

# Check runtime versions
node --version
python --version
which node   # is it the expected binary?

# Check file existence
ls -la /path/to/config
cat /path/to/config | head
```

## Technologies That Commonly Produce This
- [[Docker]], [[Kubernetes]] — Missing ENV, ConfigMap/Secret not found, wrong namespace
- [[AWS]], [[GCP]], [[Azure]] — Wrong region, missing credentials, wrong project/account
- [[OAuth]], [[OIDC]] — Client ID mismatch, redirect URI mismatch
- [[Next.js]] — Server vs client env vars (`NEXT_PUBLIC_` prefix)
- [[Terraform]] — Backend config, variable not set
- [[GitHub Actions]], [[GitLab CI]] — Missing secrets, workflow rules
- [[CUDA]] — Version mismatch with GPU architecture
- [[Elasticsearch]], [[DynamoDB]] — Index/table not found in environment

## Related Patterns
- [[The Environment Delta]] — "works on my machine" is the meta-pattern
- [[Permission & Authorization Errors]] — wrong credentials = both config and auth error
- [[Connection & Network Errors]] — wrong URL looks like network error
- [[Dependency & Import Errors]] — wrong PATH/PYTHONPATH is config issue

## My Notes

