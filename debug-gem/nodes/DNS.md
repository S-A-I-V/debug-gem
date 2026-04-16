---
tags: [networking, infrastructure, protocol]
aliases: [dns]
---
# DNS (Domain Name System)

Translates domain names to IP addresses. The "phone book" of the internet.

## Known For These Error Patterns
- [[Connection & Network Errors]] — `NXDOMAIN` (domain doesn't exist), `SERVFAIL`, timeout
- [[State & Lifecycle Errors]] — TTL caching (stale records after migration), propagation delay
- [[Configuration & Environment Errors]] — Wrong DNS server, split-horizon DNS, CNAME at zone apex

## Debug
- `nslookup domain.com` or `dig domain.com` — resolve domain
- `dig @8.8.8.8 domain.com` — query specific DNS server
- Check `/etc/resolv.conf` for DNS server configuration

## Related
- [[TLS/SSL]] — works alongside DNS for secure connections
- [[MOC — Networking & APIs]]

## My Notes

