# MOC — Security & Auth

> Error patterns for authentication, authorization, cryptography, and security vulnerabilities. Security errors are unique because they can be both loud (403 Forbidden) and silent (data breach with no error message).

---

## Authentication (AuthN) — "Who are you?"

### [[OAuth]] 2.0 / [[OIDC]]
- `invalid_grant` → [[State & Lifecycle Errors]]: Authorization code expired, already used, or refresh token revoked.
  - **Fix**: Request new authorization code. Check refresh token rotation policy.
- `invalid_client` → [[Configuration & Environment Errors]]: Client ID or client secret wrong.
  - **Fix**: Verify client credentials. Check for trailing whitespace. Verify client is registered.
- `invalid_request` → [[Syntax & Parse Errors]]: Missing required parameter or malformed request.
- `invalid_scope` → [[Permission & Authorization Errors]]: Requested scope not allowed for this client.
- `unauthorized_client` → [[Permission & Authorization Errors]]: Client not authorized for this grant type.
- `unsupported_grant_type` → [[Configuration & Environment Errors]]: Grant type not supported (e.g., using `password` grant when only `authorization_code` is allowed).
- `unsupported_response_type` → [[Configuration & Environment Errors]]: Response type not supported.
- `access_denied` → [[Permission & Authorization Errors]]: User denied consent or policy denied access.
- `server_error` → Server-side error. Retry.
- `temporarily_unavailable` → [[Connection & Network Errors]]: Auth server overloaded.
- `redirect_uri_mismatch` → [[Configuration & Environment Errors]]: Redirect URI doesn't match registered URI. Must be exact match (including trailing slash, http vs https).
- `interaction_required` → [[State & Lifecycle Errors]]: User interaction needed (consent, MFA). Can't use silent auth.
- `login_required` → [[State & Lifecycle Errors]]: User not logged in. Can't use silent auth.
- `consent_required` → [[State & Lifecycle Errors]]: User hasn't consented to requested scopes.

### OAuth Traps
- **Token storage**: Never store tokens in localStorage (XSS vulnerable). Use httpOnly cookies or secure session storage.
- **PKCE requirement**: Public clients (SPAs, mobile) MUST use PKCE. Without it, authorization code can be intercepted.
- **Token rotation race condition**: If refresh token is rotated, concurrent requests with old refresh token fail. Implement token refresh queue.
- **Scope creep**: Requesting too many scopes reduces user trust. Request minimum scopes, use incremental authorization.
- **State parameter**: Always use `state` parameter to prevent CSRF. Verify it on callback.
- **Nonce parameter**: Required for OIDC implicit flow. Prevents replay attacks.

### [[JWT]] (JSON Web Tokens)
- `TokenExpiredError` / `jwt expired` → [[State & Lifecycle Errors]]: Token past `exp` claim.
  - **Fix**: Implement refresh flow. Check clock skew between services.
- `JsonWebTokenError: invalid signature` → [[Configuration & Environment Errors]]: Wrong signing key, wrong algorithm, or token tampered.
  - **Fix**: Verify signing key matches. Check `alg` header. Ensure same key for sign and verify.
- `JsonWebTokenError: jwt malformed` → [[Serialization & Encoding Errors]]: Token string corrupted, truncated, or not a JWT.
  - **Fix**: Check token format (three base64url segments separated by dots). Check for URL encoding issues.
- `JsonWebTokenError: jwt not active` → [[State & Lifecycle Errors]]: Token's `nbf` (not before) claim is in the future.
- `JsonWebTokenError: invalid audience` → [[Configuration & Environment Errors]]: Token's `aud` claim doesn't match expected audience.
- `JsonWebTokenError: invalid issuer` → [[Configuration & Environment Errors]]: Token's `iss` claim doesn't match expected issuer.

### JWT Traps
- **`alg: none` vulnerability**: Some libraries accept `alg: none` (no signature). Always validate algorithm. Use allowlist, not denylist.
- **Symmetric vs asymmetric confusion**: If server expects RS256 (asymmetric) but attacker sends HS256 (symmetric) signed with the public key, some libraries accept it. Always specify expected algorithm.
- **Token size in cookies/headers**: JWTs can be large (especially with many claims). May exceed cookie size limit (4KB) or header size limit.
- **No revocation**: JWTs are stateless — can't be revoked before expiry. Use short expiry + refresh tokens, or maintain a revocation list.
- **Clock skew**: Different servers may have slightly different clocks. Add `clockTolerance` (e.g., 30 seconds).
- **Sensitive data in payload**: JWT payload is base64url encoded, NOT encrypted. Don't put secrets in JWT. Anyone can decode it.
- **Key rotation**: When rotating signing keys, use `kid` (key ID) header to identify which key signed the token. Support multiple keys during rotation.

### [[SAML]]
- `Invalid signature` → [[Configuration & Environment Errors]]: Certificate mismatch between IdP and SP.
- `Assertion expired` → [[State & Lifecycle Errors]]: Clock skew between IdP and SP.
- `Audience restriction not met` → [[Configuration & Environment Errors]]: SP entity ID doesn't match assertion audience.
- `InResponseTo mismatch` → [[State & Lifecycle Errors]]: Response doesn't match the original request.
- `Status: Requester` → [[Configuration & Environment Errors]]: SP sent invalid request.
- `Status: Responder` → Server error at IdP.

### SAML Traps
- **XML canonicalization**: Signature verification requires exact XML canonicalization. Whitespace changes can break signatures.
- **Metadata refresh**: IdP/SP metadata (certificates, endpoints) must be kept in sync. Automate metadata refresh.
- **XML signature wrapping attacks**: Attacker modifies unsigned parts of SAML response. Validate signature covers the assertion.
- **Replay attacks**: Use `InResponseTo` and assertion `ID` to prevent replay. Track used assertion IDs.

### API Keys
- `401 Unauthorized: Invalid API key` → [[Permission & Authorization Errors]]: Key wrong, expired, or revoked.
- `403 Forbidden: API key does not have access` → [[Permission & Authorization Errors]]: Key lacks required scope/permission.
- `429 Too Many Requests` → [[Resource Exhaustion Errors]]: Key's rate limit exceeded.

### API Key Traps
- **Key in URL**: Never put API keys in URL query parameters (logged in server logs, browser history, referrer headers). Use headers.
- **Key in client-side code**: Never embed API keys in frontend JavaScript (visible in source). Use backend proxy.
- **Key rotation**: Implement key rotation without downtime. Support multiple active keys during transition.
- **Key scope**: Use least-privilege. Create separate keys for different services/environments.

---

## Authorization (AuthZ) — "What can you do?"

### [[RBAC]] (Role-Based Access Control)
- User has role but lacks permission → Role definition incomplete or wrong role assigned.
- Permission works in dev but not prod → [[The Environment Delta]]: Different role assignments per environment.
- **Debug**: List user's roles → list role's permissions → check against required permission.
- **Trap**: Role explosion — too many fine-grained roles become unmanageable. Use role hierarchies.

### [[ABAC]] (Attribute-Based Access Control)
- Policy evaluation order matters → [[Concurrency & Race Condition Errors]]: Deny overrides allow? First match? Most specific?
- Attribute not available at evaluation time → [[State & Lifecycle Errors]]: Attribute source not reachable.
- **Trap**: Policy complexity — ABAC policies can become very complex. Test with policy simulator.

### [[PBAC]] (Policy-Based Access Control)
- `Policy evaluation error` → [[Syntax & Parse Errors]]: Policy language syntax error (Rego, Cedar, Casbin).
- `No matching policy` → [[Configuration & Environment Errors]]: No policy covers this request. Default deny.
- **Tools**: Open Policy Agent (OPA/Rego), AWS Cedar, Casbin, Zanzibar (Google).

---

## Common Security Vulnerabilities (as Error Patterns)

### Injection Attacks
- **SQL Injection** → [[The Boundary Problem]]: User input crosses the data/code boundary.
  - **Error signs**: Unexpected SQL errors in logs, data leakage, authentication bypass.
  - **Fix**: Parameterized queries / prepared statements. NEVER string concatenation. Use ORM.
  - **Test**: Try `' OR '1'='1` in input fields. Use SQLMap for automated testing.
- **XSS (Cross-Site Scripting)** → [[The Boundary Problem]]: User input rendered as HTML/JS.
  - **Stored XSS**: Malicious script saved in database, served to other users.
  - **Reflected XSS**: Malicious script in URL parameter, reflected in response.
  - **DOM XSS**: Client-side JavaScript processes untrusted data into DOM.
  - **Fix**: Output encoding (HTML entity encoding), Content Security Policy (CSP) headers, sanitize HTML input (DOMPurify).
- **Command Injection** → [[The Boundary Problem]]: User input in shell commands.
  - **Fix**: Avoid `exec()`, `system()`, `os.system()`. Use parameterized APIs. If unavoidable, use allowlist validation.
- **Path Traversal** → [[Offset & Boundary Errors]]: `../` in file paths accesses files outside intended directory.
  - **Fix**: Validate and canonicalize paths. Use `path.resolve()` and check it starts with allowed directory.
- **LDAP Injection** → [[The Boundary Problem]]: User input in LDAP queries.
  - **Fix**: Escape special LDAP characters. Use parameterized LDAP queries.
- **NoSQL Injection** → [[The Boundary Problem]]: User input in MongoDB queries (e.g., `{"$gt": ""}` in JSON body).
  - **Fix**: Validate input types. Don't pass raw user input to query operators.
- **Template Injection (SSTI)** → [[The Boundary Problem]]: User input in server-side templates.
  - **Fix**: Don't use user input in template expressions. Use sandboxed template engines.
- **Header Injection** → [[The Boundary Problem]]: User input in HTTP headers (CRLF injection).
  - **Fix**: Validate/sanitize header values. Remove newline characters.

### Cryptography Errors
- `padding error` / `BadPaddingException` → [[Serialization & Encoding Errors]]: Wrong encryption key, corrupted ciphertext, or padding oracle vulnerability.
- `unsupported algorithm` → [[Configuration & Environment Errors]]: Deprecated or unavailable crypto algorithm.
- `invalid key length` → [[Configuration & Environment Errors]]: Key size doesn't match algorithm requirement.
- `MAC check failed` → [[Serialization & Encoding Errors]]: Message authentication code verification failed. Data tampered or wrong key.

### Cryptography Traps
- **ECB mode**: Never use ECB for encryption. Patterns in plaintext visible in ciphertext. Use GCM or CBC with HMAC.
- **Hardcoded keys/IVs**: Keys and IVs must be random and secret. Never hardcode. Use key management service.
- **MD5/SHA1 for security**: Broken for collision resistance. Use SHA-256+ for hashing, bcrypt/scrypt/Argon2 for passwords.
- **Not using constant-time comparison**: String comparison short-circuits on first mismatch. Timing attack reveals correct bytes. Use `hmac.compare_digest()` or `crypto.timingSafeEqual()`.
- **Random number generation**: Use cryptographically secure RNG (`secrets` in Python, `crypto.randomBytes` in Node, `SecureRandom` in Java). Never use `Math.random()` for security.
- **Password storage**: Never store plaintext passwords. Never use simple hashing (MD5, SHA). Use bcrypt, scrypt, or Argon2 with salt.
- **Key derivation**: Don't use encryption keys directly from passwords. Use PBKDF2, scrypt, or Argon2 for key derivation.

### Session Management
- **Session fixation** → [[State & Lifecycle Errors]]: Attacker sets session ID before login. After login, attacker has authenticated session.
  - **Fix**: Regenerate session ID after login.
- **Session hijacking** → [[Permission & Authorization Errors]]: Attacker steals session cookie.
  - **Fix**: Use `Secure`, `HttpOnly`, `SameSite` cookie attributes. Use HTTPS only.
- **CSRF (Cross-Site Request Forgery)** → [[The Boundary Problem]]: Attacker tricks user's browser into making authenticated request.
  - **Fix**: CSRF tokens, `SameSite=Strict` or `SameSite=Lax` cookies, check `Origin`/`Referer` headers.
- **Session timeout** → [[State & Lifecycle Errors]]: Session expires but UI doesn't reflect it.
  - **Fix**: Implement idle timeout and absolute timeout. Redirect to login on 401.

### CORS (Cross-Origin Resource Sharing)
- `Access-Control-Allow-Origin` missing → [[Permission & Authorization Errors]]: Server doesn't send CORS headers.
- `Access-Control-Allow-Origin: *` with credentials → [[Configuration & Environment Errors]]: Can't use wildcard with `credentials: 'include'`. Must specify exact origin.
- Preflight (OPTIONS) request failing → [[Configuration & Environment Errors]]: Server not handling OPTIONS method.
- **CORS is browser-enforced**: Server-to-server requests don't have CORS restrictions. CORS only applies to browser JavaScript.
- **Fix**: Configure server to send appropriate CORS headers. Use a CORS middleware in your framework.

### Content Security Policy (CSP)
- `Refused to execute inline script because it violates the following Content Security Policy directive` → [[Permission & Authorization Errors]]: CSP blocking inline scripts.
  - **Fix**: Use nonces (`script-src 'nonce-abc123'`) or hashes. Move inline scripts to external files.
- `Refused to load the script 'X' because it violates the following Content Security Policy directive` → [[Permission & Authorization Errors]]: Script source not in CSP allowlist.
  - **Fix**: Add source to CSP directive. Use `report-uri` to monitor violations before enforcing.

---

## Security Headers Checklist
1. `Strict-Transport-Security` (HSTS) — Force HTTPS.
2. `Content-Security-Policy` (CSP) — Control resource loading.
3. `X-Content-Type-Options: nosniff` — Prevent MIME type sniffing.
4. `X-Frame-Options: DENY` — Prevent clickjacking.
5. `X-XSS-Protection: 0` — Disable browser XSS filter (CSP is better).
6. `Referrer-Policy: strict-origin-when-cross-origin` — Control referrer information.
7. `Permissions-Policy` — Control browser features (camera, microphone, geolocation).
8. `Cross-Origin-Opener-Policy` (COOP) — Isolate browsing context.
9. `Cross-Origin-Resource-Policy` (CORP) — Control cross-origin resource loading.
10. `Cross-Origin-Embedder-Policy` (COEP) — Control cross-origin embedding.

---

## API Security Checklist
1. **Authentication**: Is the caller who they claim to be? → Check token/key validity.
2. **Authorization**: Can this caller do this action on this resource? → Check permissions/scopes/policies.
3. **Input validation**: Is the input safe and valid? → Sanitize, validate, parameterize. Reject unexpected fields.
4. **Rate limiting**: Is the caller abusing the API? → Implement per-key and per-IP rate limits.
5. **Encryption in transit**: Is data protected on the wire? → TLS 1.2+ everywhere.
6. **Encryption at rest**: Is stored data protected? → Encrypt sensitive data in database.
7. **Logging & audit**: Are security events recorded? → Log auth failures, permission denials, unusual patterns.
8. **Error handling**: Do error messages leak information? → Don't expose stack traces, SQL queries, or internal paths.
9. **Dependency security**: Are dependencies free of known vulnerabilities? → `npm audit`, `pip audit`, Snyk, Dependabot.
10. **Secrets management**: Are secrets stored securely? → Use secret managers, not env files in repos.
