---
tags: [blockchain, typescript, library]
aliases: [viem]
---
# Viem

Modern [[TypeScript]]-first library for [[Ethereum]]. Lightweight, tree-shakeable, type-safe alternative to [[Ethers.js]] and [[Web3.js]]. Built for performance and developer experience.

## Known For These Error Patterns
- [[Type & Casting Errors]] — Strong TypeScript types catch errors at compile time (ABI type inference)
- [[Connection & Network Errors]] — Transport errors (HTTP/WebSocket), chain not configured
- [[Configuration & Environment Errors]] — Wrong chain config, missing transport, account not set
- [[State & Lifecycle Errors]] — Nonce conflicts, pending transaction issues

## Common Errors
- `ContractFunctionExecutionError` — Contract call reverted. Contains decoded revert reason if ABI provided.
- `TransactionExecutionError` — Transaction failed. Check inner `cause` for details.
- `InvalidAddressError` — Checksum mismatch or invalid address format. Use `getAddress()` to normalize.
- `ChainMismatchError` — Wallet connected to different chain than client configured for.
- `HttpRequestError` / `WebSocketRequestError` — RPC endpoint unreachable or returned error.
- `EstimateGasExecutionError` — Gas estimation failed (transaction would revert). Simulate first.
- `InsufficientFundsError` — Account balance too low for gas + value.

## Key Concepts
- **Client types**: `publicClient` (read), `walletClient` (write), `testClient` (anvil/hardhat)
- **Type-safe ABIs**: Import ABI as `const` for full type inference on contract functions
- **Actions**: Composable functions instead of class methods
- **Transports**: HTTP, WebSocket, IPC, custom

## Common Gotchas
- Must configure chain explicitly — no auto-detection from RPC
- `account` must be set on walletClient for write operations
- BigInt everywhere — Viem uses native BigInt, not ethers' BigNumber class
- ABI must be `as const` for TypeScript type inference to work
- Batch JSON-RPC: enable with `batch: { multicall: true }` for automatic batching
- Simulating before sending: use `simulateContract` before `writeContract` to catch reverts early

## Migration from Ethers.js
| Ethers.js | Viem |
|-----------|------|
| `new ethers.JsonRpcProvider(url)` | `createPublicClient({ transport: http(url) })` |
| `contract.functionName(args)` | `client.readContract({ address, abi, functionName, args })` |
| `ethers.parseEther("1.0")` | `parseEther("1.0")` |
| `BigNumber` | Native `BigInt` |
| Class-based | Function-based (tree-shakeable) |

## Related
- [[Ethers.js]], [[Web3.js]] — older alternatives
- [[Ethereum]], [[Solidity]] — what it connects to
- [[TypeScript]] — Viem is TypeScript-first
- [[MOC — Blockchain & Web3]]

## My Notes

