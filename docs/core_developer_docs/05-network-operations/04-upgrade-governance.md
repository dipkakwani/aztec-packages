---
title: Upgrade and Governance
description: How Aztec protocol contracts are upgraded on L1, the governance process for proposing and ratifying changes, L1 contract addresses, and the emergency escape hatch mechanism.
sidebar_position: 4
tags: [internals, governance, upgrade, l1-contracts, escape-hatch, protocol]
---

# Upgrade and Governance

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- **L1 contract architecture**: the set of deployed protocol contracts (`Rollup`, `Inbox`, `Outbox`, `FeeJuice`, `Registry`, `GovernanceProposer`) and their relationships
- **The `Registry` contract**: how it tracks the canonical Rollup address and enables contract upgrades without changing downstream integrations
- **Upgrade process**: how a new Rollup contract version is proposed, the voting/timelock mechanism, and how the Registry is updated to point to the new address
- **The `GovernanceProposer`**: who can propose upgrades, what a proposal looks like, and the required quorum
- **Backwards compatibility**: how the sequencer handles the epoch boundary when an upgrade takes effect
- **The escape hatch**: what the escape hatch is (a mechanism to exit funds if the network halts), how users trigger it, and the conditions under which it activates
- **`suspendSentinelDuringEscapeHatch`**: why validator duties are suspended during escape hatch activation
- **Emergency response**: the process for responding to critical bugs — which roles can act unilaterally vs which require governance
- **L1 contract addresses**: where to find canonical addresses for each network (devnet, testnet, mainnet)
- **ABI stability**: which L1 interfaces are stable vs subject to change across protocol versions

## Prerequisites

- [L1 Finality](../01-transaction-lifecycle/08-l1-finality.md)
- [Validator Registration and Slashing](./03-validator-slashing.md)
