---
tags: [error-pattern, universal-failure-pattern]
aliases: [connection-errors, network-errors, timeout, unreachable]
---
# Connection & Network Errors

> Can't reach the other side. The network path between two systems is broken or degraded.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. Communication between two endpoints failed — the target is unreachable, unresponsive, or the connection was interrupted.

## The Pattern
System A tries to talk to System B, but the communication fails. Could be:
- Target doesn't exist or isn't listening
- Network path is blocked (firewall, security group, DNS)
- Target is too slow (timeout)
- Connection was established but dropped mid-communication
- SSL/TLS handshake failure

## Universal Symptoms
- `Connection refused` (ECONNREFUSED) — target not listening on that port
- `Connection timed out` (ETIMEDOUT) — no response at all (firewall, wrong IP)
- `Connection reset` (ECONNRESET) — target forcibly closed connection
- `Name resolution failed` (ENOTFOUND) — DNS can't resolve hostname
- `502 Bad Gateway`, `503 Service Unavailable`, `504 Gateway Timeout`
- `UNAVAILABLE`, `DEADLINE_EXCEEDED` (gRPC)
- `No route to host`, `Network unreachable`
- `SSL handshake failure`, `certificate verify failed`

## Diagnostic Decision Tree
1. **CAN YOU REACH THE HOST?** `ping host` or `telnet host port`
2. **IS DNS RESOLVING?** `nslookup host` or `dig host`
3. **IS THE PORT OPEN?** `nc -zv host port` or `curl -v host:port`
4. **IS SOMETHING BLOCKING?** Firewall, security group, NetworkPolicy, proxy
5. **IS THE TARGET HEALTHY?** Is the service actually running and accepting connections?
6. **IS IT INTERMITTENT?** Load balancer routing to unhealthy backend, network congestion

## Common Causes

### Target Not Listening
- Service crashed or hasn't started yet
- Service listening on wrong port or interface (127.0.0.1 vs 0.0.0.0)
- Container/pod not ready yet

### DNS Issues
- Wrong hostname or typo
- DNS propagation not complete after change
- Split-horizon DNS (different resolution inside vs outside VPC)
- `/etc/resolv.conf` pointing to wrong DNS server

### Network Blocking
- Firewall rules (iptables, security groups, NACLs)
- Kubernetes NetworkPolicy blocking traffic
- Corporate proxy not configured
- VPN not connected

### Timeouts
- Target overloaded and too slow to respond
- Network congestion or packet loss
- Connection pool exhausted on target
- Client timeout too aggressive

### TLS/SSL
- Certificate expired or self-signed
- Hostname doesn't match certificate CN/SAN
- TLS version mismatch (client requires TLS 1.3, server only has 1.2)
- Missing intermediate certificate in chain

## Fix Strategies
1. **Verify connectivity** — ping, telnet, curl from the SOURCE machine (not your laptop)
2. **Check from inside the network** — kubectl exec into pod, SSH into container
3. **Trace the path** — traceroute, VPC flow logs, firewall logs
4. **Retry with backoff** — transient network issues resolve themselves
5. **Circuit breaker** — stop hammering a dead service, fail fast
6. **Health checks** — detect unhealthy backends before routing traffic

## Debug Commands
```bash
# DNS resolution
dig hostname
nslookup hostname

# Port connectivity
nc -zv hostname port
telnet hostname port
curl -v https://hostname

# Network path
traceroute hostname
mtr hostname

# Local listeners
ss -tlnp              # what's listening
lsof -i :8080        # what's on port 8080

# TLS diagnosis
openssl s_client -connect hostname:443
curl -vI https://hostname 2>&1 | grep -A5 "SSL"
```

## Technologies That Commonly Produce This
- [[DNS]] — resolution failures, propagation delays
- [[TLS-SSL]] — handshake failures, certificate issues
- [[Docker]], [[Kubernetes]] — container networking, service discovery
- [[AWS]], [[GCP]], [[Azure]] — VPC, security groups, load balancers
- [[gRPC]] — UNAVAILABLE, DEADLINE_EXCEEDED
- [[WebSocket]] — handshake failures, connection drops
- [[Redis]], [[MongoDB]], [[PostgreSQL]] — connection pool exhaustion
- [[Ethereum]], [[IPFS]] — node connectivity, RPC endpoint issues
- [[REST API]] — 502/503/504 errors

## Related Patterns
- [[Resource Exhaustion Errors]] — connection pool exhaustion causes connection errors
- [[Configuration & Environment Errors]] — wrong URL/port is config, not network
- [[Permission & Authorization Errors]] — firewall blocking is network, 401/403 is auth
- [[The Boundary Problem]] — network is the ultimate boundary

## My Notes

