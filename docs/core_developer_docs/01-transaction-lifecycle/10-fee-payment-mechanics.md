---
title: Fee Payment Mechanics
description: How Aztec's two-token fee system works — Fee Juice for L1 gas, the public fee payment flow, fee abstraction via fee-paying contracts, and how fees are settled across private and public execution phases.
sidebar_position: 10
tags: [internals, fees, fee-juice, gas, mana, fee-payer]
---

# Fee Payment Mechanics

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- The two-phase fee model: fees are **reserved** at the start of a TX and **settled** at the end based on actual gas consumed
- **Fee Juice**: the native fee token bridged from L1 ETH, used to pay for block space
- The **Mana** model: how L2 gas (computation) and DA gas (data availability) are metered separately and converted to Fee Juice
- **Fee-paying contracts**: how account abstraction enables third parties to pay fees on behalf of users (sponsored transactions, fee abstraction)
- The `FeePaymentMethod` interface in `aztec.js` and how wallets select a fee payer
- How the sequencer validates that fees are sufficient before including a TX
- How fee payment interacts with revertible vs non-revertible TX phases — fees are always deducted even if the app logic reverts
- Public fee settlement: the `teardown` phase that executes after all public logic and handles actual fee deduction
- Gas estimation: how the PXE simulates gas usage before sending
- Fee market dynamics: priority fees, base fees, and how the sequencer orders TXs

## Prerequisites

- [Public Execution](./05-public-execution.md)
- [User Invocation](./01-user-invocation.md) — where fee parameters are specified in `aztec.js`

## Related Topics

- [AVM — Gas Model](../02-zk-circuits-state/05-avm.md) — the two-dimensional gas accounting
- [Sequencer Block Building](./04-sequencer-block-building.md) — how the sequencer prioritizes TXs by fee
