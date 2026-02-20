---
title: The Anchor Block Concept
description: Why private functions execute against a historical state snapshot, how the archive tree pins the anchor block, and the security implications for stale reads and tx expiration.
sidebar_position: 9
tags: [internals, anchor-block, historical-state, archive-tree, expiration]
---

# The Anchor Block Concept

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- Why private circuits cannot read the current L2 state — they execute against a **historical anchor block** (a finalized snapshot)
- How the PXE selects the anchor block when `proveTx()` is called
- The **Archive Tree**: a Merkle tree of historical block hashes that the kernel circuit uses to prove the anchor block is legitimate
- Why this design is necessary: the sequencer learns your TX at inclusion time, which is after you proved it — the proof must be valid against a known-good state
- The **tx expiration** mechanism: every TX includes `max_block_number` (the latest block at which the proof is still valid); if the sequencer can't include it before that block, the TX is discarded
- Edge cases: what happens if state changes between proof generation and inclusion, why public state reads in private functions require `DelayedPublicMutable`
- Practical implications for wallet UX: initial sync before proving, how stale anchor blocks affect proof validity window
- The relationship between anchor block age and frontrunning risk

## Prerequisites

- [PXE Private Execution](./02-pxe-private-execution.md)
- [Contract Identity — DelayedPublicMutable](../03-keys-accounts-privacy/04-contract-identity.md)

## Related Topics

- [Sequencer Block Building](./04-sequencer-block-building.md) — how the sequencer validates anchor block age
- [L1 Finality](./08-l1-finality.md) — the three finality stages and their relationship to anchor block selection
