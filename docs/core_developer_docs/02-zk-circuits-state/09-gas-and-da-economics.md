---
title: Gas and Data Availability Economics
description: Aztec's two-dimensional gas model (L2 Gas + DA Gas), how Mana ties them together, EIP-4844 blob pricing for data availability, and the economics of private vs public transactions.
sidebar_position: 11
tags: [internals, gas, da-gas, mana, eip-4844, blobs, economics]
---

# Gas and Data Availability Economics

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- **Two-dimensional gas**: L2 Gas (computation: opcodes, hash operations) vs DA Gas (data availability: how many bytes go on L1)
- **Mana**: the unified fee unit that converts L2 Gas and DA Gas into Fee Juice for payment
- How the AVM meters both gas dimensions simultaneously per opcode
- **EIP-4844 blob economics**: each Aztec L2 block publishes note ciphertext as a blob; blob prices fluctuate based on beacon chain demand
- Why private transactions are DA-heavy: encrypted note ciphertexts and nullifiers must be published on L1 so recipients can find them
- Why public transactions are L2-heavy but DA-light: public state diffs are small
- Gas limits at each level: per-opcode limits, per-transaction limits, per-block limits
- How the sequencer enforces gas limits during block building
- Gas estimation in the PXE: simulating L2 and DA gas before a transaction is sent
- **Gas padding**: adding dummy notes/nullifiers for privacy costs extra DA gas — the privacy/cost tradeoff

## Prerequisites

- [AVM](./05-avm.md) — the two-dimensional gas metering per opcode
- [L1 Finality](../01-transaction-lifecycle/08-l1-finality.md) — blob publishing on L1

## Related Topics

- [Fee Payment Mechanics](../01-transaction-lifecycle/10-fee-payment-mechanics.md)
- [Note Encryption](../03-keys-accounts-privacy/02-note-encryption.md) — why fixed-length note ciphertexts cost more DA gas
