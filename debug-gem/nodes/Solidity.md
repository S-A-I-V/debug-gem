---
tags: [blockchain, smart-contracts, ethereum, language]
aliases: [solidity, sol]
---
# Solidity

Smart contract language for EVM (Ethereum Virtual Machine). Bugs can lose real money. Transactions are irreversible.

## Known For These Error Patterns
- [[Resource Exhaustion Errors]] — Out of gas, contract size limit, stack too deep
- [[Permission & Authorization Errors]] — Caller not owner/admin, missing access control
- [[Concurrency & Race Condition Errors]] — Reentrancy attacks, front-running, flash loan exploits
- [[Offset & Boundary Errors]] — Integer overflow (pre-0.8), array out of bounds
- [[State & Lifecycle Errors]] — Contract paused, proxy storage collision, nonce issues

## Critical Security Checklist
- [ ] Checks-Effects-Interactions pattern (prevent reentrancy)
- [ ] Access control on all admin functions
- [ ] No `tx.origin` for authentication (use `msg.sender`)
- [ ] Check return values of low-level calls
- [ ] Use OpenZeppelin contracts for standard patterns
- [ ] Fuzz test and formal verify critical logic

## Related
- [[Ethereum]] — primary blockchain
- [[Ethers.js]], [[Web3.js]], [[Viem]] — JavaScript libraries
- [[MOC — Blockchain & Web3]]

## My Notes

