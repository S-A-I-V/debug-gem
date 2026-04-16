---
tags: [blockchain, javascript, library]
aliases: [ethers, ethersjs]
---
# Ethers.js

[[JavaScript]] library for interacting with [[Ethereum]]. Clean API, TypeScript support, widely used.

## Common Errors
- `CALL_EXCEPTION` — Transaction reverted. Decode error data.
- `INSUFFICIENT_FUNDS` — Not enough ETH for gas + value
- `UNPREDICTABLE_GAS_LIMIT` — Gas estimation failed (tx would revert)
- `invalid BigNumber string` — Passing float where integer expected. Use `ethers.parseEther()`

## Related
- [[Web3.js]], [[Viem]] — alternatives
- [[Solidity]], [[Ethereum]] — what it connects to
- [[MOC — Blockchain & Web3]]

## My Notes

