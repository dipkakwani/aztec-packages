---
title: Running a Sequencer Node
description: Hardware requirements, L1 registration, slot timing configuration, block building parameters, and how sequencers handle reorgs and missed slots.
sidebar_position: 1
tags: [internals, sequencer, block-building, registration, slot-timing]
---

# Running a Sequencer Node

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- Hardware requirements for running a sequencer (CPU, memory, disk for world-state trees)
- L1 registration: staking, the `Rollup.addValidator()` call, and how the committee is selected from registered validators
- Slot timing: how `SLOT_DURATION` and `EPOCH_DURATION` affect when the sequencer must act
- The `SequencerClient` startup: syncing from genesis, catching up to the chain tip, handling reorgs
- Block building configuration: `MAX_TX_PER_BLOCK`, blob size limits, tx selection strategy
- How the sequencer handles a missed slot (network partition, slow proving): what gets slashed vs what does not
- The `propose()` → L1 transaction flow: timing requirements, gas costs, what happens if L1 is congested
- Monitoring: key metrics to watch (slot utilization, reorg depth, mempool size)
- Common operational issues: world-state tree corruption, L1 RPC failures, P2P peer starvation

## Prerequisites

- [Sequencer Block Building](../01-transaction-lifecycle/04-sequencer-block-building.md)
- [Validator Attestation](../01-transaction-lifecycle/06-validator-attestation.md)
