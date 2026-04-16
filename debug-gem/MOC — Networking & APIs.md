# MOC — Networking & APIs

> Error patterns for HTTP, REST, GraphQL, gRPC, WebSockets, DNS, TLS, and network protocols. This is where [[The Boundary Problem]] is most visible — every API call crosses a trust and format boundary.

---

## HTTP Status Code Taxonomy (Complete)

### 1xx — Informational
- `100 Continue` → Server received headers, client should send body. Not an error.
- `101 Switching Protocols` → WebSocket upgrade. Not an error.
- `103 Early Hints` → Preload resources. Not an error.

### 2xx — Success
- `200 OK` → Success. But check response body — some APIs return 200 with error in body ([[The Silent Failure]]).
- `201 Created` → Resource created successfully.
- `202 Accepted` → Request accepted for async processing. Result not yet available.
- `204 No Content` → Success, no body. Don't try to parse response body.

### 3xx — Redirection
- `301 Moved Permanently` → Resource moved. Update your URL. Browsers cache this aggressively.
- `302 Found` → Temporary redirect. Don't cache.
- `304 Not Modified` → Cached version is still valid. Not an error.
- `307 Temporary Redirect` → Like 302 but preserves HTTP method.
- `308 Permanent Redirect` → Like 301 but preserves HTTP method.
- **Redirect loops** → [[Configuration & Environment Errors]]: A redirects to B, B redirects to A. Check server config, load balancer, CDN.

### 4xx — Client Errors
- `400 Bad Request` → [[Syntax & Parse Errors]] or [[Type & Casting Errors]]: Malformed request body, invalid parameters, missing required fields.
  - **Debug**: Check request body format (JSON valid?), Content-Type header, required fields, parameter types.
- `401 Unauthorized` → [[Permission & Authorization Errors]]: Missing or invalid authentication. Token expired, API key wrong, no auth header.
  - **Fix**: Check `Authorization` header format (`Bearer <token>`, `Basic <base64>`). Verify token not expired. Check API key.
- `403 Forbidden` → [[Permission & Authorization Errors]]: Authenticated but not authorized. Or resource doesn't exist (security through obscurity).
  - **Fix**: Check permissions/scopes. Check if resource exists (some APIs return 403 instead of 404).
- `404 Not Found` → [[Configuration & Environment Errors]]: Wrong URL, resource deleted, or routing misconfigured.
  - **Debug**: Check URL path, API version prefix, trailing slash, case sensitivity.
- `405 Method Not Allowed` → [[Configuration & Environment Errors]]: Using GET instead of POST, etc.
  - **Fix**: Check API docs for correct HTTP method. Check `Allow` response header.
- `406 Not Acceptable` → [[Serialization & Encoding Errors]]: Server can't produce response in format client requested (Accept header).
- `408 Request Timeout` → [[Connection & Network Errors]]: Client took too long to send complete request.
- `409 Conflict` → [[Concurrency & Race Condition Errors]] or [[State & Lifecycle Errors]]: Resource state conflict. Concurrent modification, duplicate creation.
  - **Fix**: Use optimistic concurrency (ETags, version numbers). Retry with fresh data.
- `410 Gone` → [[State & Lifecycle Errors]]: Resource permanently deleted. Unlike 404, this is intentional.
- `411 Length Required` → [[Configuration & Environment Errors]]: Missing Content-Length header.
- `412 Precondition Failed` → [[Concurrency & Race Condition Errors]]: ETag/If-Match condition not met. Resource was modified.
- `413 Payload Too Large` → [[Resource Exhaustion Errors]]: Request body exceeds server limit.
  - **Fix**: Compress payload, use chunked upload, increase server limit.
- `414 URI Too Long` → [[Resource Exhaustion Errors]]: URL exceeds server limit. Use POST with body instead of GET with query params.
- `415 Unsupported Media Type` → [[Serialization & Encoding Errors]]: Wrong Content-Type header.
  - **Fix**: Set `Content-Type: application/json` for JSON, `multipart/form-data` for file uploads.
- `416 Range Not Satisfiable` → [[Offset & Boundary Errors]]: Range header requests bytes beyond file size.
- `422 Unprocessable Entity` → [[Type & Casting Errors]]: Valid syntax but semantic errors. Validation failure.
  - **Common in**: REST APIs with input validation. Check response body for field-level errors.
- `429 Too Many Requests` → [[Resource Exhaustion Errors]]: Rate limit hit.
  - **Fix**: Check `Retry-After` header. Implement exponential backoff with jitter. Cache responses. Use bulk APIs.
- `431 Request Header Fields Too Large` → [[Resource Exhaustion Errors]]: Headers (often cookies) too large.
- `451 Unavailable For Legal Reasons` → [[Permission & Authorization Errors]]: Content blocked for legal reasons (geo-restriction, DMCA).

### 5xx — Server Errors
- `500 Internal Server Error` → Catch-all server error. Check server logs for the real error.
  - **Debug**: This is the server's problem. Check server logs, not client code. If you control the server, add better error handling.
- `501 Not Implemented` → [[Configuration & Environment Errors]]: Server doesn't support the HTTP method for this endpoint.
- `502 Bad Gateway` → [[Connection & Network Errors]]: Reverse proxy/load balancer can't reach upstream server.
  - **Causes**: Upstream server crashed, wrong upstream address, upstream timeout, upstream returned invalid response.
- `503 Service Unavailable` → [[Resource Exhaustion Errors]] or [[State & Lifecycle Errors]]: Server overloaded, in maintenance, or not ready.
  - **Fix**: Check `Retry-After` header. Wait and retry. Check server health.
- `504 Gateway Timeout` → [[Connection & Network Errors]]: Upstream server too slow to respond.
  - **Fix**: Increase proxy timeout, optimize upstream, add caching, use async processing.
- `507 Insufficient Storage` → [[Resource Exhaustion Errors]]: Server storage full.
- `508 Loop Detected` → [[Configuration & Environment Errors]]: Infinite redirect/proxy loop.
- `520-530` (Cloudflare-specific) → Various Cloudflare-origin communication errors.

---

## [[REST API]] Patterns

### Common Issues
- **Versioning mismatch**: Client using v1, server expects v2 → [[Configuration & Environment Errors]].
  - **Fix**: Check API version in URL path (`/v2/`) or header (`Accept: application/vnd.api+json;version=2`).
- **Pagination cursor expired** → [[State & Lifecycle Errors]]: Cursor-based pagination token expired or invalidated.
  - **Fix**: Handle expired cursor gracefully. Restart from beginning or use offset-based fallback.
- **CORS errors** → [[Permission & Authorization Errors]]: Browser blocking cross-origin request.
  - `Access to XMLHttpRequest at 'X' from origin 'Y' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header`
  - `Access to XMLHttpRequest blocked by CORS policy: Response to preflight request doesn't pass access control check`
  - **Fix**: Server must send correct headers. Cannot be fixed client-side. Add `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`, `Access-Control-Allow-Credentials`.
  - **Note**: Preflight (OPTIONS) request must also return CORS headers. Many frameworks handle this automatically.
- **Content-Type mismatch** → [[Serialization & Encoding Errors]]: Sending JSON without `Content-Type: application/json`.
- **Trailing slash inconsistency** → [[Configuration & Environment Errors]]: `/api/users` vs `/api/users/` — some servers treat these differently.
- **Query parameter encoding** → [[Serialization & Encoding Errors]]: Special characters in query params not URL-encoded.
- **Idempotency** → [[Concurrency & Race Condition Errors]]: POST requests not idempotent. Duplicate submissions create duplicate resources.
  - **Fix**: Use idempotency keys (`Idempotency-Key` header). Use PUT for idempotent updates.
- **N+1 API calls** → [[Resource Exhaustion Errors]]: Fetching list then fetching each item individually.
  - **Fix**: Use batch endpoints, include/embed related resources, use GraphQL.

---

## [[GraphQL]]

### Common Errors
- `Cannot query field "X" on type "Y"` → [[Syntax & Parse Errors]]: Field doesn't exist on type. Schema mismatch.
- `Variable "$x" of required type "X!" was not provided` → [[Null & Undefined Reference Errors]]: Required variable missing.
- `Variable "$x" of type "X" used in position expecting type "Y"` → [[Type & Casting Errors]]: Variable type mismatch.
- `Field "X" argument "Y" of type "Z" is required, but it was not provided` → [[Null & Undefined Reference Errors]]: Required argument missing.
- `Syntax Error: Expected Name, found }` → [[Syntax & Parse Errors]]: GraphQL query syntax error.
- `Syntax Error: Unexpected <EOF>` → [[Syntax & Parse Errors]]: Incomplete query.
- `Cannot return null for non-nullable field X.Y` → [[Null & Undefined Reference Errors]]: Resolver returned null for non-nullable field.
- `PersistedQueryNotFound` → [[Configuration & Environment Errors]]: Persisted query hash not registered on server.

### GraphQL-Specific Issues
- **N+1 query problem** → [[Resource Exhaustion Errors]]: Each field resolver makes a database query.
  - **Fix**: Use DataLoader for batching and caching within a request.
- **Over-fetching in nested queries** → [[Resource Exhaustion Errors]]: Deeply nested query fetches too much data.
  - **Fix**: Set query depth limits, query complexity limits, and field-level cost analysis.
- **Schema stitching/federation conflicts** → [[Dependency & Import Errors]]: Type conflicts between subgraphs.
- **Subscription connection management** → [[Connection & Network Errors]]: WebSocket connection drops, reconnection logic.
- **Partial errors** → [[The Silent Failure]]: GraphQL can return data AND errors simultaneously. Always check `errors` array.
- **Introspection in production** → [[Permission & Authorization Errors]]: Disable introspection in production to prevent schema discovery.

---

## [[gRPC]]

### Status Codes
- `OK (0)` → Success.
- `CANCELLED (1)` → [[State & Lifecycle Errors]]: Client cancelled the request.
- `UNKNOWN (2)` → Unknown error. Check server logs.
- `INVALID_ARGUMENT (3)` → [[Type & Casting Errors]]: Invalid request parameter.
- `DEADLINE_EXCEEDED (4)` → [[Connection & Network Errors]]: Request timed out.
- `NOT_FOUND (5)` → [[Configuration & Environment Errors]]: Resource not found.
- `ALREADY_EXISTS (6)` → [[State & Lifecycle Errors]]: Resource already exists.
- `PERMISSION_DENIED (7)` → [[Permission & Authorization Errors]]: Not authorized.
- `RESOURCE_EXHAUSTED (8)` → [[Resource Exhaustion Errors]]: Rate limit or resource limit.
- `FAILED_PRECONDITION (9)` → [[State & Lifecycle Errors]]: System not in required state.
- `ABORTED (10)` → [[Concurrency & Race Condition Errors]]: Transaction aborted due to conflict.
- `OUT_OF_RANGE (11)` → [[Offset & Boundary Errors]]: Value out of valid range.
- `UNIMPLEMENTED (12)` → [[Configuration & Environment Errors]]: Method not implemented on server.
- `INTERNAL (13)` → Server error. Check server logs.
- `UNAVAILABLE (14)` → [[Connection & Network Errors]]: Server not reachable.
- `DATA_LOSS (15)` → [[Serialization & Encoding Errors]]: Unrecoverable data loss.
- `UNAUTHENTICATED (16)` → [[Permission & Authorization Errors]]: Not authenticated.

### gRPC-Specific Issues
- **HTTP/2 requirement** → [[Configuration & Environment Errors]]: gRPC requires HTTP/2. Proxies/load balancers must support it.
  - **Fix**: Configure proxy for HTTP/2 (Nginx: `grpc_pass`, Envoy: native support, ALB: gRPC target group).
- **Protobuf backward compatibility** → [[Serialization & Encoding Errors]]: Breaking changes in .proto file.
  - **Rules**: Never reuse field numbers. Add fields as optional. Don't change field types.
- **Streaming error handling** → [[State & Lifecycle Errors]]: Errors in streaming RPCs need special handling.
- **Deadline propagation** → [[Connection & Network Errors]]: Deadlines should propagate through service chain. Set reasonable deadlines.
- **Load balancing** → [[Configuration & Environment Errors]]: gRPC uses long-lived connections. Need L7 load balancing, not L4.
- **Large messages** → [[Resource Exhaustion Errors]]: Default max message size is 4MB. Increase with `MaxRecvMsgSize`/`MaxSendMsgSize`.

---

## [[WebSocket]]

### Common Errors
- `WebSocket connection to 'X' failed: Error during WebSocket handshake: Unexpected response code: 403` → [[Permission & Authorization Errors]]: Auth failed or CORS issue.
- `WebSocket connection to 'X' failed: Error during WebSocket handshake: Unexpected response code: 400` → [[Configuration & Environment Errors]]: Server doesn't support WebSocket at this endpoint.
- `WebSocket connection to 'X' failed: Error during WebSocket handshake: Unexpected response code: 502` → [[Connection & Network Errors]]: Proxy not configured for WebSocket.
- `CloseEvent code 1000` → Normal closure. Not an error.
- `CloseEvent code 1001` → Going away (server shutting down, page navigating away).
- `CloseEvent code 1006` → [[Connection & Network Errors]]: Abnormal closure. No close frame received. Network issue.
- `CloseEvent code 1008` → [[Permission & Authorization Errors]]: Policy violation.
- `CloseEvent code 1009` → [[Resource Exhaustion Errors]]: Message too large.
- `CloseEvent code 1011` → Server error. Check server logs.
- `CloseEvent code 1013` → [[Resource Exhaustion Errors]]: Server overloaded. Try again later.
- `CloseEvent code 1015` → [[Connection & Network Errors]]: TLS handshake failure.

### WebSocket-Specific Issues
- **Proxy support** → [[Configuration & Environment Errors]]: Many proxies don't support WebSocket upgrade. Configure proxy for WS.
- **Heartbeat/ping-pong** → [[Connection & Network Errors]]: Without heartbeat, idle connections may be killed by proxies/firewalls.
- **Reconnection** → [[Connection & Network Errors]]: Implement exponential backoff reconnection. Don't reconnect immediately (thundering herd).
- **Message ordering** → [[Concurrency & Race Condition Errors]]: Messages are ordered within a connection, but reconnection may cause gaps.
- **Authentication** → [[Permission & Authorization Errors]]: Can't set custom headers in browser WebSocket API. Use query params or first message for auth.
- **Scaling** → [[Resource Exhaustion Errors]]: Each WebSocket connection holds a TCP connection. Need sticky sessions or pub/sub for multi-server.

---

## [[DNS]]

### Common Errors
- `NXDOMAIN` → Domain doesn't exist. Check domain name spelling, registration status.
- `SERVFAIL` → DNS server error. Try different DNS server (8.8.8.8, 1.1.1.1).
- `REFUSED` → DNS server refused query. Check DNS server configuration.
- `TIMEOUT` → DNS server not responding. Network issue or DNS server down.

### DNS-Specific Issues
- **TTL caching** → [[State & Lifecycle Errors]]: After DNS change, old records cached for TTL duration. Lower TTL before migration.
- **Propagation delay** → [[State & Lifecycle Errors]]: DNS changes take time to propagate globally (minutes to hours).
- **Split-horizon DNS** → [[The Environment Delta]]: Different DNS responses for internal vs external queries.
- **CNAME at zone apex** → [[Configuration & Environment Errors]]: Can't use CNAME at root domain. Use ALIAS/ANAME or A record.
- **DNS rebinding** → [[Permission & Authorization Errors]]: Security attack using DNS to bypass same-origin policy.

---

## [[TLS/SSL]]

### Common Errors
- `certificate has expired` → [[State & Lifecycle Errors]]: Renew certificate. Use auto-renewal (Let's Encrypt, cert-manager).
- `certificate verify failed` / `unable to get local issuer certificate` → [[Configuration & Environment Errors]]: CA not trusted. Missing intermediate certificate.
  - **Fix**: Include full certificate chain. Install CA bundle. For self-signed: add to trust store.
- `handshake failure` → [[Connection & Network Errors]]: Protocol version or cipher suite mismatch.
  - **Fix**: Check supported TLS versions (1.2 minimum). Check cipher suites.
- `hostname mismatch` / `certificate is not valid for the requested host` → [[Configuration & Environment Errors]]: Certificate CN/SAN doesn't match requested domain.
  - **Fix**: Issue certificate with correct domain names. Check for www vs non-www.
- `self-signed certificate` → [[Configuration & Environment Errors]]: Certificate not issued by trusted CA.
  - **Fix**: Use Let's Encrypt for free trusted certificates. For internal: add CA to trust store.
- `certificate is not yet valid` → [[Configuration & Environment Errors]]: Certificate's `notBefore` date is in the future. Check system clock.
- `SSL routines:ssl3_get_record:wrong version number` → [[Configuration & Environment Errors]]: Connecting with SSL/TLS to non-SSL port (or vice versa).
- `no protocols available` → [[Configuration & Environment Errors]]: No common TLS protocol version between client and server.

### TLS Debug
- `openssl s_client -connect host:443` — test TLS connection, see certificate chain.
- `openssl s_client -connect host:443 -servername host` — test with SNI.
- `openssl x509 -in cert.pem -text -noout` — inspect certificate details.
- `curl -v https://host` — verbose output shows TLS handshake.
- `ssllabs.com/ssltest` — comprehensive TLS configuration test.
