# MOC — Blockchain & Web3

> Error patterns for blockchain, smart contracts, DeFi, NFTs, and decentralized systems. This domain has unique constraints: transactions are irreversible, gas costs money, and bugs can lose real funds.

---

## [[Solidity]] / EVM Smart Contracts

### Transaction Errors
- `execution reverted` → Generic revert. Decode the revert reason from the data field.
  - With reason: `execution reverted: Ownable: caller is not the owner` → [[Permission & Authorization Errors]]
  - With reason: `execution reverted: ERC20: transfer amount exceeds balance` → [[Offset & Boundary Errors]]
  - With reason: `execution reverted: ERC20: insufficient allowance` → [[Permission & Authorization Errors]]
  - With reason: `execution reverted: Pausable: paused` → [[State & Lifecycle Errors]]
  - With reason: `execution reverted: ReentrancyGuard: reentrant call` → [[Concurrency & Race Condition Errors]]
  - Without reason: Could be `require(false)` without message, or low-level call failure.
- `out of gas` → [[Resource Exhaustion Errors]]: Transaction ran out of gas.
  - **Fix**: Optimize gas usage (avoid loops over unbounded arrays, use `mapping` over `array`, minimize storage writes). Increase gas limit. Use `estimateGas()` + 20% buffer.
- `intrinsic gas too low` → [[Configuration & Environment Errors]]: Gas limit below minimum (21000 for simple transfer).
- `nonce too low` → [[State & Lifecycle Errors]]: Transaction with this nonce already mined.
  - **Fix**: Get current nonce with `getTransactionCount('pending')`. Don't reuse nonces.
- `nonce too high` → [[State & Lifecycle Errors]]: Gap in nonce sequence. Previous transaction still pending.
  - **Fix**: Wait for pending transactions or cancel them (send 0-value tx with same nonce, higher gas).
- `replacement transaction underpriced` → [[State & Lifecycle Errors]]: Trying to replace pending tx but new gas price not high enough (must be >= 10% higher).
- `insufficient funds for gas * price + value` → [[Resource Exhaustion Errors]]: Account balance too low.
- `max fee per gas less than block base fee` → [[Configuration & Environment Errors]]: EIP-1559 base fee higher than your max fee.
- `transaction type not supported` → [[Configuration & Environment Errors]]: Network doesn't support EIP-1559 or other tx types.
- `exceeds block gas limit` → [[Resource Exhaustion Errors]]: Single transaction can't exceed block gas limit.

### Smart Contract Compilation Errors
- `DeclarationError: Undeclared identifier` → [[Dependency & Import Errors]]: Missing import or variable not declared.
- `TypeError: Type X is not implicitly convertible to expected type Y` → [[Type & Casting Errors]]: Type mismatch.
- `TypeError: Member "X" not found or not visible after argument-dependent lookup` → [[Type & Casting Errors]]: Method doesn't exist on type.
- `Warning: Function state mutability can be restricted to view/pure` → Not error, but indicates function doesn't modify state.
- `CompilerError: Stack too deep` → [[Resource Exhaustion Errors]]: Too many local variables (EVM stack limit is 16).
  - **Fix**: Use structs to group variables, split into helper functions, use `via-ir` compiler pipeline.
- `Warning: Contract code size exceeds 24576 bytes` → [[Resource Exhaustion Errors]]: Contract too large to deploy (EIP-170 limit).
  - **Fix**: Split into libraries, use proxy pattern, optimize code.
- `ParserError: Expected ';' but got 'X'` → [[Syntax & Parse Errors]]: Solidity syntax error.
- `Warning: Unused local variable` → Dead code. Clean up.
- `Warning: SPDX license identifier not provided` → Missing `// SPDX-License-Identifier: MIT` at top.

### Security Vulnerabilities (as Error Patterns)
- **Reentrancy** → [[Concurrency & Race Condition Errors]]: External call before state update allows recursive calls.
  - **Fix**: Checks-Effects-Interactions pattern. Use `ReentrancyGuard` from OpenZeppelin. Update state BEFORE external calls.
  - **Example**: The DAO hack (2016) — $60M stolen via reentrancy.
- **Integer overflow/underflow** → [[Offset & Boundary Errors]]: Arithmetic wraps around.
  - **Fix**: Solidity >=0.8.0 has built-in overflow checks. For older versions, use SafeMath.
- **Front-running** → [[Concurrency & Race Condition Errors]]: Attacker sees pending tx and submits their own with higher gas.
  - **Fix**: Commit-reveal schemes, use Flashbots, submarine sends.
- **Oracle manipulation** → [[Concurrency & Race Condition Errors]]: Attacker manipulates price oracle in same transaction.
  - **Fix**: Use TWAP (Time-Weighted Average Price), Chainlink oracles, multiple oracle sources.
- **Flash loan attacks** → [[Concurrency & Race Condition Errors]]: Borrow large amount, manipulate market, profit, repay — all in one transaction.
  - **Fix**: Use TWAP oracles, add time delays, check for flash loan in same block.
- **Access control missing** → [[Permission & Authorization Errors]]: Critical function callable by anyone.
  - **Fix**: Use `onlyOwner`, OpenZeppelin `AccessControl`, multi-sig for admin functions.
- **Delegatecall to untrusted contract** → [[Permission & Authorization Errors]]: Delegatecall executes code in caller's context. Malicious code can modify storage.
- **tx.origin authentication** → [[Permission & Authorization Errors]]: `tx.origin` can be spoofed via intermediary contract. Use `msg.sender`.
- **Unchecked external call** → [[The Silent Failure]]: Low-level `.call()` returns `(bool, bytes)`. If you don't check the bool, failure is silent.
  - **Fix**: Always check return value: `(bool success, ) = addr.call(...); require(success);`
- **Storage collision in proxies** → [[State & Lifecycle Errors]]: Upgradeable proxy storage layout changed between versions.
  - **Fix**: Use storage gaps (`uint256[50] __gap;`), EIP-1967 storage slots, OpenZeppelin upgradeable contracts.
- **Denial of Service (DoS)** → [[Resource Exhaustion Errors]]: Attacker makes function unusable.
  - **Push vs Pull**: Don't loop over unbounded arrays to send ETH. Use pull pattern (users withdraw).
  - **Block gas limit**: Function that iterates over growing array eventually exceeds block gas limit.
- **Signature replay** → [[Concurrency & Race Condition Errors]]: Same signature used multiple times.
  - **Fix**: Include nonce and chain ID in signed message. Use EIP-712 typed data.
- **Selfdestruct** → [[State & Lifecycle Errors]]: `selfdestruct` sends ETH to address, bypassing `receive()`/`fallback()`. Can break contracts that check `address(this).balance`.
  - **Note**: `selfdestruct` deprecated in EIP-6049, behavior changed in Dencun upgrade.

---

## [[Ethereum]] / Node Errors

- `header not found` → [[Connection & Network Errors]]: Node not synced to requested block.
- `missing trie node` → [[State & Lifecycle Errors]]: Node pruned state data. Need archive node.
- `transaction pool is full` → [[Resource Exhaustion Errors]]: Node's mempool is full.
- `already known` → [[State & Lifecycle Errors]]: Transaction already in mempool.
- `fee too low` → [[Configuration & Environment Errors]]: Gas price below network minimum.
- `invalid sender` → [[Permission & Authorization Errors]]: Transaction signature invalid.
- `invalid chain id` → [[Configuration & Environment Errors]]: Wrong network (mainnet vs testnet).

---

## [[Web3.js]] / [[Ethers.js]] / [[Viem]]

### Ethers.js Errors
- `CALL_EXCEPTION` → Transaction reverted. Decode the error data.
- `INSUFFICIENT_FUNDS` → [[Resource Exhaustion Errors]]: Not enough ETH for gas + value.
- `NONCE_EXPIRED` → [[State & Lifecycle Errors]]: Nonce already used.
- `REPLACEMENT_UNDERPRICED` → [[State & Lifecycle Errors]]: Replacement tx gas too low.
- `UNPREDICTABLE_GAS_LIMIT` → [[Resource Exhaustion Errors]]: Gas estimation failed (tx would revert).
- `NETWORK_ERROR` → [[Connection & Network Errors]]: RPC connection failed.
- `SERVER_ERROR` → [[Connection & Network Errors]]: RPC server returned error.
- `TIMEOUT` → [[Connection & Network Errors]]: RPC request timed out.
- `INVALID_ARGUMENT` → [[Type & Casting Errors]]: Wrong argument type (e.g., passing float where BigNumber expected).
- `NUMERIC_FAULT: overflow` → [[Offset & Boundary Errors]]: Number too large for type.
- `NUMERIC_FAULT: underflow` → [[Offset & Boundary Errors]]: Number too small (precision loss).
- `BUFFER_OVERRUN` → [[Offset & Boundary Errors]]: ABI decoding ran past end of data.
- `could not detect network` → [[Connection & Network Errors]]: Can't connect to RPC or network detection failed.
- `network does not support ENS` → [[Configuration & Environment Errors]]: ENS only on mainnet (and some testnets).
- `invalid BigNumber string` → [[Type & Casting Errors]]: Passing float or invalid string where BigNumber expected.
  - **Fix**: Use `ethers.parseEther("1.0")` not `1.0`. Wei values must be integers.
- `invalid address` → [[Type & Casting Errors]]: Address not valid (wrong length, bad checksum).
- `missing revert data` → [[The Silent Failure]]: Transaction reverted but no reason provided.

### Web3.js Errors
- `Returned error: execution reverted` → Same as Ethers.js CALL_EXCEPTION.
- `Transaction has been reverted by the EVM` → Transaction mined but reverted.
- `Invalid JSON RPC response` → [[Connection & Network Errors]]: RPC returned non-JSON (HTML error page, empty response).
- `connection not open on send()` → [[Connection & Network Errors]]: WebSocket connection closed.

---

## DeFi-Specific Patterns

- **Slippage too high** → [[The Boundary Problem]]: Price moved between quote and execution.
  - **Fix**: Set appropriate slippage tolerance. Use limit orders. Check liquidity depth.
- **Impermanent loss** → [[The Silent Failure]]: LP position worth less than holding. Not an error, but unexpected for new users.
- **Oracle stale price** → [[State & Lifecycle Errors]]: Price feed not updated. Check `updatedAt` timestamp.
  - **Fix**: Add staleness check: `require(block.timestamp - updatedAt < MAX_STALENESS)`.
- **Liquidation** → [[Offset & Boundary Errors]]: Collateral ratio below threshold. Position liquidated.
- **MEV (Maximal Extractable Value)** → [[Concurrency & Race Condition Errors]]: Miners/validators reorder transactions for profit.
  - **Fix**: Use Flashbots, private mempools, MEV-resistant protocols.
- **Sandwich attack** → [[Concurrency & Race Condition Errors]]: Attacker front-runs and back-runs your swap.
  - **Fix**: Set tight slippage, use private transactions, use DEX aggregators with MEV protection.

---

## [[IPFS]] / Decentralized Storage

- `CID not found` → [[Connection & Network Errors]]: Content not pinned or gateway timeout.
  - **Fix**: Pin content to pinning service (Pinata, Infura, web3.storage). Use multiple gateways.
- `dag/get timeout` → [[Resource Exhaustion Errors]]: Large DAG traversal.
- `block not found` → [[Connection & Network Errors]]: Block not available on connected peers.
- `invalid CID` → [[Type & Casting Errors]]: CID string malformed.

---

## NFT-Specific Errors

- `ERC721: token already minted` → [[State & Lifecycle Errors]]: Token ID already exists.
- `ERC721: transfer of token that is not own` → [[Permission & Authorization Errors]]: Caller doesn't own the token.
- `ERC721: approve to caller` → [[State & Lifecycle Errors]]: Can't approve yourself.
- `ERC721: invalid token ID` → [[Offset & Boundary Errors]]: Token doesn't exist.
- `ERC721Metadata: URI query for nonexistent token` → [[Offset & Boundary Errors]]: Querying metadata for unminted token.
- **Metadata not loading** → [[Connection & Network Errors]]: IPFS gateway slow, centralized server down, or URI wrong.
- **Royalty not enforced** → [[The Silent Failure]]: ERC-2981 is voluntary. Marketplaces may not honor it.

---

## Development & Testing

### Hardhat Errors
- `HardhatError: HH700: Artifact not found` → [[Dependency & Import Errors]]: Contract not compiled. Run `npx hardhat compile`.
- `ProviderError: HttpProviderError` → [[Connection & Network Errors]]: RPC connection issue.
- `InvalidInputError: Transaction gas limit is X and target gas limit is Y` → [[Configuration & Environment Errors]]: Gas limit exceeds block gas limit.
- `Error: VM Exception while processing transaction: reverted with reason string 'X'` → Read the reason string.

### Foundry Errors
- `[FAIL. Reason: X]` → Test assertion failed. Read the reason.
- `EvmError: OutOfGas` → [[Resource Exhaustion Errors]]: Test transaction ran out of gas.
- `EvmError: Revert` → Transaction reverted. Check the revert reason.
- `Error: Failed to get EIP-1559 fees` → [[Configuration & Environment Errors]]: Network doesn't support EIP-1559.

### Testing Best Practices
- **Fuzz testing**: Use Foundry's fuzzer or Echidna to find edge cases.
- **Invariant testing**: Define properties that must always hold. Test with random sequences of actions.
- **Fork testing**: Test against mainnet state using `forge test --fork-url`.
- **Gas snapshots**: Track gas usage changes between commits.
- **Formal verification**: Use Certora, Halmos, or KEVM for mathematical proofs of correctness.
