---
title: Working with Barretenberg
description: Building the C++ proving backend, using the bb CLI for proof generation and verification, inspecting circuit witnesses, and debugging constraint failures locally.
sidebar_position: 4
tags: [internals, barretenberg, bb, cpp, proving, witness, debugging]
---

# Working with Barretenberg

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- **Building Barretenberg**: CMake build, presets (`clang16`, `wasm`), and how to link against it from TypeScript via the `bb` binary
- **The `bb` CLI**: key subcommands for a monorepo contributor:
  - `bb prove` — generate a proof from a bytecode + witness file
  - `bb verify` — verify a proof against a VK
  - `bb write_vk` — generate a verification key from a circuit
  - `bb check_circuit` — find which constraint is violated (invaluable for debugging)
  - `bb gates` — count gates in a circuit
- **ACIR → witness → proof pipeline**: how Noir output flows through `acvm`, `bb`, and back up to the TypeScript layer
- **Debugging constraint failures**: `bb check_circuit` outputs the failing gate index; how to trace that back to Noir source
- **Inspecting proof bytes**: the proof serialization format (per [Barretenberg Prover Internals](../02-zk-circuits-state/07-barretenberg-prover.md)) and how to deserialize a proof for inspection
- **Running Honk tests directly**: the C++ test suite in `barretenberg/cpp/src/barretenberg/ultra_honk/`, how to run with `cmake --build . --target honk_tests`
- **Performance profiling**: using `tracy` or `perf` to find bottlenecks in the proving pipeline
- **WASM build**: how the browser-compatible `barretenberg.wasm` is built and used in the PXE browser target

## Prerequisites

- [Barretenberg Prover Internals](../02-zk-circuits-state/07-barretenberg-prover.md)
- [Sandbox and Local Dev](./01-sandbox-local-dev.md)
