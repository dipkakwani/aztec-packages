---
title: Running a Prover Node
description: Architecture of the broker/agent proving system, hardware requirements for Barretenberg proof generation, epoch proof submission timing, and how prover nodes coordinate with sequencers.
sidebar_position: 2
tags: [internals, prover-node, broker, agent, barretenberg, gpu, epoch-proof]
---

# Running a Prover Node

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- The **broker/agent architecture**: the broker coordinates work, agents do the actual proving (can be on different machines or GPUs)
- Hardware requirements: CPU requirements for UltraHonk, GPU acceleration support, memory for SRS (structured reference string)
- SRS loading: where the trusted setup parameters live, how they're cached, and what happens on first startup
- The epoch proving timeline: how the `ProvingOrchestrator` builds the proof tree across an entire epoch (32 blocks)
- Job queue priorities: which circuits get proved first (TX Base → TX Merge → Block Root → Epoch Root)
- How prover nodes learn about new blocks: subscribing to the sequencer's block events
- `submitEpochRootProof()`: the L1 call that finalizes an epoch; gas costs and timing requirements
- **Multiple prover nodes**: how to run competing provers (first valid proof wins), the incentive structure
- Monitoring: key metrics (jobs queued, jobs completed, time-to-proof per circuit)
- Common issues: insufficient SRS size for large circuits, broker/agent connectivity, epoch timeout

## Prerequisites

- [Proving Infrastructure](../01-transaction-lifecycle/07-proving-infrastructure.md)
- [Barretenberg Prover Internals](../02-zk-circuits-state/07-barretenberg-prover.md)
- [L1 Finality](../01-transaction-lifecycle/08-l1-finality.md)
