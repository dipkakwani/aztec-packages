---
title: Debugging Private Execution
description: How to inspect ACVM execution traces, mock oracles in unit tests, read PXE logs to trace note discovery failures, and identify kernel witness constraint violations.
sidebar_position: 2
tags: [internals, debugging, acvm, oracles, pxe-logs, witness]
---

# Debugging Private Execution

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- **Constraint failures**: how to interpret the "constraint not satisfied" error from the kernel circuit and trace it back to a specific gate
- **ACVM execution traces**: enabling verbose ACVM logging to see which oracle calls fire and what they return
- **Mock oracle patterns**: how to write unit tests that mock oracles (`MockOracle`) without running a full PXE
- **Note discovery failures**: reading PXE `LogService` logs to debug why a note isn't being found (wrong tag index, wrong sender-recipient pair, mismatched contract address)
- **Nullifier collisions**: diagnosing double-spend errors vs note-already-nullified errors
- **Kernel witness debugging**: using `bb` to inspect the kernel circuit witness when a proof fails to generate
- **`aztec-nargo test`**: running Noir unit tests for private functions with oracle injection
- Common pitfalls:
  - Missing `#[view]` annotation causing state mutation in simulation
  - Off-by-one in tag index (happens after a dropped TX)
  - Using `context.msg_sender()` incorrectly across nested calls
  - Forgetting to register sender in `addTaggingSecrets` before expecting note discovery

## Prerequisites

- [PXE Private Execution](../01-transaction-lifecycle/02-pxe-private-execution.md)
- [PXE Internals](../03-keys-accounts-privacy/05-pxe-internals.md)
