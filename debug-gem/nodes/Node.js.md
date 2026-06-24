---
tags: [runtime, backend, javascript, server]
aliases: [nodejs, node]
---
# Node.js

JavaScript runtime built on V8 engine. Single-threaded event loop for async I/O. Dominant in web backends, CLI tools, and serverless.

## Known For These Error Patterns
- [[Resource Exhaustion Errors]] — Heap out of memory, event loop blocking, file descriptor limits
- [[Connection & Network Errors]] — `ECONNREFUSED`, `ECONNRESET`, `ETIMEDOUT`
- [[Dependency & Import Errors]] — `MODULE_NOT_FOUND`, ESM vs CJS confusion, require cycles
- [[Null & Undefined Reference Errors]] — `Cannot read properties of undefined`
- [[State & Lifecycle Errors]] — Unhandled promise rejections, callback hell state management

## Common Errors
- `FATAL ERROR: CALL_AND_RETRY_LAST Allocation failed - JavaScript heap out of memory` — Increase with `--max-old-space-size=4096` or fix the leak
- `Error: listen EADDRINUSE: address already in use :::3000` — Port already taken. Kill the other process or use different port.
- `UnhandledPromiseRejectionWarning` → `UnhandledPromiseRejection` (Node 15+) — Missing `.catch()` or try/catch around await
- `Error [ERR_REQUIRE_ESM]` — Trying to `require()` an ESM-only package. Use dynamic `import()` or switch to ESM.
- `Error: EMFILE: too many open files` — File descriptor limit hit. Use `graceful-fs` or increase `ulimit -n`.
- `Error: connect ECONNREFUSED 127.0.0.1:5432` — Database/service not running locally
- `RangeError: Maximum call stack size exceeded` — Infinite recursion or too-deep recursion

## Event Loop Gotchas
- **Blocking the event loop** — CPU-intensive sync operations freeze EVERYTHING. Use Worker Threads for CPU work.
- **`setImmediate` vs `process.nextTick`** — nextTick fires before I/O callbacks, setImmediate fires after. nextTick can starve I/O.
- **Microtask queue priority** — Promises resolve before setTimeout/setImmediate callbacks
- **Unhandled rejection** — In Node 15+, unhandled promise rejection terminates the process

## Common Gotchas
- ESM vs CJS — can't freely mix. `import` for ESM, `require` for CJS. Use `"type": "module"` in package.json for ESM.
- `__dirname` not available in ESM — use `import.meta.url` and `fileURLToPath`
- `package.json` `"exports"` field — controls what others can import from your package
- `process.env` values are ALWAYS strings — `process.env.PORT` is `"3000"` not `3000`
- Stream backpressure — if you pipe a fast readable to a slow writable without respecting backpressure, memory balloons
- Cluster mode — single Node process uses one CPU. Use `cluster` module or PM2 for multi-core.

## Debug Tools
- `node --inspect` — Chrome DevTools debugger
- `node --prof` — CPU profiling
- `node --heap-snapshot-signal=SIGUSR2` — trigger heap snapshot
- `clinic.js` — performance profiling suite (Doctor, Flame, Bubbleprof)
- `0x` — flame graph generator
- `why-is-node-running` — find what's keeping the process alive

## Key Commands
```bash
node --max-old-space-size=4096 app.js   # increase heap
node --inspect app.js                     # debugger
node -e "console.log(process.versions)"  # runtime versions
node --trace-warnings app.js             # trace warning origins
```

## Related
- [[JavaScript]] — Node.js runs JavaScript
- [[npm]], [[yarn]], [[pnpm]] — package managers
- [[Next.js]] — full-stack framework on Node
- [[MOC — Languages & Runtimes]]

## My Notes

