---
title: Private DeFi and the UTXO Model
description: How Aztec's UTXO-style note model differs from ERC-20 account balances, what this means for building private DeFi protocols, and the design patterns required for private AMMs, lending, and NFTs.
sidebar_position: 7
tags: [internals, utxo, notes, private-defi, amm, tokens, design-patterns]
---

# Private DeFi and the UTXO Model

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- **UTXO vs account model** in depth: why Ethereum's `mapping(address => uint256)` is incompatible with privacy, and why Aztec uses UTXO-style notes
- The "shielded balance" mental model: your balance is the sum of all your unspent note values — no contract can read this, not even the contract you're transacting with
- **Note composability**: how private token transfers are atomic (create receiver note + burn sender notes in the same TX)
- **Private AMMs**: the fundamental difficulty — an AMM needs to know the exchange rate, but the exchange rate must be public for both parties to agree on it. `DelayedPublicMutable` is the solution.
- **Private lending**: collateral notes, debt tracking, liquidation in a UTXO world
- **Private NFTs**: single-note ownership transfer, why NFT enumeration is incompatible with privacy
- **Note fragmentation**: the UX problem when a user has many small notes instead of one large one; how wallets handle consolidation
- **Partial fills**: why order books are harder in private systems (you can't see how much of an order is remaining)
- The **private → public crossover**: when and why private logic must call into public (e.g., partial fills that require coordination with other users)
- Design patterns: partial notes, token notes, programmable notes with custom nullifier logic
- What "mixing" means in this context and why Aztec's note model provides stronger privacy than Tornado Cash's fixed-denomination model

## Prerequisites

- [Account Abstraction](./03-account-abstraction.md) — the UTXO note model introduction
- [Contract Identity — DelayedPublicMutable](./04-contract-identity.md)

## Related Topics

- [Public Execution](../01-transaction-lifecycle/05-public-execution.md) — how private→public calls work
- [Note Encryption](./02-note-encryption.md) — why only the recipient can discover their notes
