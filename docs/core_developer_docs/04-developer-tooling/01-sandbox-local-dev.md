---
title: Sandbox and Local Development
description: How to run the Aztec sandbox locally, the services it starts, how TypeScript tests connect to it, and how to iterate quickly on contract development.
sidebar_position: 1
tags: [internals, sandbox, local-dev, setup, docker]
---

# Sandbox and Local Development

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- What the sandbox is: a single Docker Compose stack running a local L1 (Anvil), Aztec node, sequencer, prover (mock), and PXE
- `aztec start` anatomy: what each service does and which ports they listen on
- How to use the TypeScript `createSandbox()` helper in e2e tests
- The `AztecNodeApiSchema` and how TypeScript clients talk to the node over JSON-RPC
- Hot-reloading contracts: `aztec-nargo compile` + deploying a new class without restarting the sandbox
- Using `aztec-wallet` for manual TX submission during development
- Common sandbox issues: port conflicts, stale Docker volumes, anvil block time affecting test timing
- The mock prover: why sandbox uses a fast fake prover and what it skips (no actual ZK proofs in dev)
- Running with a real prover locally: what to set in `PROVER_ENABLED` and expected proving times

## Prerequisites

- [Transaction Lifecycle Overview](../01-transaction-lifecycle/index.md)

## Related Topics

- [Debugging Private Execution](./02-debugging-private-execution.md)
