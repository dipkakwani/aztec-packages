---
title: Testing Strategies
description: Unit testing Noir circuits with oracle injection, integration testing against the sandbox, e2e test patterns, and how to structure test coverage for private and public contract logic.
sidebar_position: 3
tags: [internals, testing, unit-tests, e2e, noir, sandbox]
---

# Testing Strategies

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- **Noir unit tests** (`#[test]` in Noir): how to write and run circuit-level tests without a full PXE, using `aztec-nargo test`
- **Oracle injection in tests**: how Noir's `OracleMock` pattern lets you supply return values for oracle calls inside circuit tests
- **TypeScript unit tests** for `yarn-project/`: the mock patterns for PXE, node, and oracle dependencies; how `MockProxy` from the `jest` layer is used
- **Sandbox integration tests**: `createSandbox()` + `waitForPXE()` startup pattern; deploying a contract and running assertions against its state
- **E2E test patterns** in `yarn-project/end-to-end/`: what each test fixture provides, how to share accounts and deployed contracts across test cases
- **Testing private note discovery**: the pattern for sending a note as Alice and verifying Bob's PXE discovers it (requires `addTaggingSecrets` setup)
- **Testing revert behavior**: asserting that a TX reverts in the public phase but private state is still applied correctly
- **Circuit constraint count tests**: how to assert a contract function stays under a circuit size limit (important for client-side proving time budgets)
- CI patterns: which tests run in which CI jobs, how to add a new e2e test to the test matrix

## Prerequisites

- [Sandbox and Local Dev](./01-sandbox-local-dev.md)
- [Debugging Private Execution](./02-debugging-private-execution.md)
