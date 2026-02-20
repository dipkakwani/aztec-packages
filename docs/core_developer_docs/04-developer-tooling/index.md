---
title: Developer Tooling
description: Local development environment, sandbox setup, debugging private execution, testing strategies for private and public circuits, and common pitfalls for monorepo contributors.
sidebar_position: 0
tags: [internals, tooling, sandbox, debugging, testing]
---

# Developer Tooling

:::caution Coming Soon
This section is a planned addition to the core developer documentation. The pages below will cover the practical tooling every monorepo contributor needs to work effectively with the Aztec stack.
:::

## Pages in This Section

| Page | What It Will Cover |
|------|--------------------|
| [Sandbox and Local Dev](./01-sandbox-local-dev.md) | Running `aztec start`, docker sandbox, nargo build, TypeScript test harness |
| [Debugging Private Execution](./02-debugging-private-execution.md) | PXE logs, ACVM execution traces, kernel witness debugging, oracle mock patterns |
| [Testing Strategies](./03-testing-strategies.md) | Unit tests for circuits (Noir), integration tests (e2e), sandbox testing patterns |
| [Working with Barretenberg](./04-working-with-barretenberg.md) | Building the C++ proving backend, `bb` CLI, generating test proofs locally |

## Key Tools

- **`aztec start`** — launches the local sandbox (full node + PXE + sequencer + prover)
- **`aztec-nargo`** — compiles Noir contracts and generates ACIR + ABI
- **`aztec-wallet`** — CLI wallet for sending TXs in development
- **`bb`** — Barretenberg C++ CLI for proof generation, verification, and witness debugging
- **`yarn test`** in `yarn-project/` — runs TypeScript unit and integration tests
- **e2e tests** in `yarn-project/end-to-end/` — full-stack tests against the sandbox

## What Comes Next

[Network Operations](../05-network-operations/index.md) covers running production sequencer and prover nodes.
