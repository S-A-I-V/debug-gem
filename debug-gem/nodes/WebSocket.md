---
tags: [networking, realtime, protocol]
aliases: [websocket, ws, wss]
---
# WebSocket

Full-duplex communication protocol over TCP. Persistent connection for real-time data (chat, live updates, gaming).

## Known For These Error Patterns
- [[Connection & Network Errors]] — Handshake failures, abnormal closure (code 1006), proxy issues
- [[Resource Exhaustion Errors]] — Message too large, too many connections
- [[Permission & Authorization Errors]] — Auth during handshake, CORS-like issues

## Common Gotchas
- Many proxies don't support WebSocket upgrade — configure proxy for WS
- Without heartbeat/ping-pong, idle connections killed by firewalls
- Can't set custom headers in browser WebSocket API — use query params or first message for auth
- Each connection holds a TCP socket — scaling requires sticky sessions or pub/sub

## Related
- [[gRPC]] — also supports streaming
- [[MOC — Networking & APIs]]

## My Notes

