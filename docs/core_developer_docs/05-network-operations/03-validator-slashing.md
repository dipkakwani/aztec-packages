---
title: Validator Registration and Slashing
description: How validators stake and register on L1, committee selection mechanics, attestation duties per slot, and the conditions that trigger equivocation or inactivity slashing.
sidebar_position: 3
tags: [internals, validator, staking, committee, attestation, slashing, equivocation]
---

# Validator Registration and Slashing

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- **Staking and registration**: the `Rollup.addValidator()` L1 call, minimum stake amount, and how the validator set is managed on-chain
- **Committee selection**: how the epoch committee is deterministically selected from registered validators using the previous epoch's randomness
- **Proposer selection per slot**: the VRF-based `getProposerAt(slot)` mechanism that selects one committee member as the block proposer
- **Attestation duties**: what validators must do each slot (re-execute the block, sign the attestation, broadcast via P2P), the timing window for attestations
- **BFT threshold**: how many attestations the proposer needs before submitting to L1 (2/3 of committee)
- **Equivocation slashing**: what it means to equivocate (sign two different blocks for the same slot), how it's detected on L1, and the penalty
- **Inactivity slashing**: what happens if a validator misses many attestations consecutively
- **Withdrawal**: how validators exit the set, the unbonding period, and timing constraints
- **Monitoring your validator**: which metrics to watch (missed slots, attestation inclusion rate, committee participation)
- **Sentinel and escape hatch**: the `suspendSentinelDuringEscapeHatch` mechanism and when it applies

## Prerequisites

- [Validator Attestation](../01-transaction-lifecycle/06-validator-attestation.md)
- [Running a Sequencer](./01-running-a-sequencer.md)
