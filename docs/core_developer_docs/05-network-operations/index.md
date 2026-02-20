---
title: Network Operations
description: Running production Aztec sequencer and prover nodes — registration, configuration, hardware requirements, epoch management, and slashing risk.
sidebar_position: 0
tags: [internals, network-ops, sequencer, prover-node, registration]
---

# Network Operations

:::caution Coming Soon
This section is a planned addition to the core developer documentation. The pages below will cover what monorepo engineers and network operators need to understand when running or debugging Aztec network nodes.
:::

## Pages in This Section

| Page | What It Will Cover |
|------|--------------------|
| [Running a Sequencer Node](./01-running-a-sequencer.md) | Hardware requirements, registration on L1, slot/epoch timing, block building configuration |
| [Running a Prover Node](./02-running-a-prover-node.md) | Proof coordination, broker/agent architecture, GPU requirements, epoch proof submission |
| [Validator Registration and Slashing](./03-validator-slashing.md) | Staking, committee selection, attestation duties, equivocation and inactivity slashing |
| [Upgrade and Governance](./04-upgrade-governance.md) | Protocol contract upgrades, governance process, L1 contract addresses, emergency escape hatch |

## Architecture Context

A production Aztec network has three distinct node roles:

| Role | Responsibility | Count |
|------|----------------|-------|
| **Sequencer** | Proposes blocks, orders TXs, handles public execution | Many (rotating) |
| **Validator** | Re-executes and attests to proposed blocks | Many (committee) |
| **Prover Node** | Generates epoch root proofs for L1 submission | Few (broker/agent model) |

All three roles are separate processes but can be co-located. The sequencer is elected each slot via VRF; the validator committee is selected per epoch.

## What Comes Next

Return to the [Transaction Lifecycle](../01-transaction-lifecycle/index.md) to see how these nodes interact in the full TX flow.
