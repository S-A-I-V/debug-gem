---
tags: [blockchain, javascript, library]
aliases: [web3, web3js]
---
# Web3.js

Original [[JavaScript]] library for [[Ethereum]] interaction. Older than [[Ethers.js]], larger API surface. v4.x is a major rewrite with TypeScript support and modular architecture.

## Known For These Error Patterns
- [[Connection & Network Errors]] — `connection not open`, provider disconnected, WebSocket timeout
- [[Type & Casting Errors]] — `invalid argument` (wrong types passed to contract methods)
- [[State & Lifecycle Errors]] — Transaction stuck pending, nonce gaps
- [[Resource Exhaustion Errors]] — Gas estimation failures, `INSUFFICIENT_FUNDS`
- [[Configuration & Environment Errors]] — Wrong provider URL, chain ID mismatch

## Common Errors
- `connection not open on send()` — WebSocket provider disconnected. Implement auto-reconnect.
- `Transaction has been reverted by the EVM` — Contract call failed. Decode revert reason from receipt.
- `invalid JSON-RPC response` — RPC endpoint returned non-JSON (HTML error page, proxy block)
- `Returned values aren't valid, did it run Out of Gas?` — Calling function that reverts, or wrong ABI
- `nonce too low` — Transaction with this nonce already mined. Increment nonce.
- `replacement transaction underpriced` — Trying to replace pending tx with insufficient gas bump (need >= 10% higher)
- `exceeds block gas limit` — Transaction gas exceeds block limit

## Key Differences: v1.x vs v4.x
| Feature | v1.x (legacy) | v4.x (current) |
|---------|---------------|-----------------|
| Types | No TypeScript | Full TypeScript |
| Architecture | Monolithic | Modular packages |
| BigNumber | `BN.js` | Native `BigInt` |
| Providers | Built-in | Plugin-based |
| Bundle size | Large | Tree-shakeable |

## Common Gotchas
- Provider management — Web3.js doesn't handle provider disconnection gracefully. Always add reconnect logic for WebSocket providers.
- Event subscriptions leak memory if not unsubscribed — especially on page navigation in dApps
- `eth.getAccounts()` returns empty array if MetaMask locked — handle gracefully
- Gas estimation can be unreliable — add 20-30% buffer above `estimateGas()` result
- Large dependency tree in v1.x — bundle size bloat for frontend dApps
- `web3.eth.Contract` instances cache provider — if provider changes, recreate contract instance

## Debug
- Enable provider debug: `web3.providers.HttpProvider(url, { debug: true })`
- Check connection: `web3.eth.net.isListening()`
- Verify chain: `web3.eth.getChainId()`
- Decode error: `web3.eth.abi.decodeParameter('string', errorData.slice(10))`

## Related
- [[Ethers.js]], [[Viem]] — modern alternatives (smaller, better TypeScript)
- [[Ethereum]], [[Solidity]] — what it connects to
- [[JavaScript]] — Web3.js is JavaScript-native
- [[MOC — Blockchain & Web3]]

## My Notes

