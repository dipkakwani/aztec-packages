---
title: Trust Boundaries and Oracle Security
description: What the ZK proof actually guarantees vs what requires trusting the PXE, how oracle data cannot forge valid proofs, and the librarian model for understanding the oracle trust boundary.
sidebar_position: 10
tags: [internals, trust-model, oracles, security, pxe, cryptographic-boundary]
---

# Trust Boundaries and Oracle Security

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- The **cryptographic boundary**: what a valid kernel proof makes impossible vs what requires trusting your software stack
- The **oracle trust model**: oracles are like a librarian — they provide data, but the circuit verifies that data satisfies the constraints. A lying oracle cannot produce a valid proof.
- Detailed walkthrough of which oracle calls are verified in-circuit and which are unconstrained:
  - `getNotes()` — returned notes are verified via Merkle membership proofs (verified)
  - `getNullifierMembership()` — proven against the nullifier tree (verified)
  - `getAuthWitness()` — the witness must satisfy `is_valid_impl()` in-circuit (verified)
  - `getContractInstance()` — must be registered in the instance nullifier tree (verified)
  - `getLogs()` — note discovery is unconstrained (trusted)
  - `getBlockHeader()` — the archive tree Merkle proof is verified (verified)
- The **information leakage boundary**: what the PXE can learn about you even without breaking proofs (which contracts you call, when you transact, gas patterns)
- Multi-party trust: running your own Aztec node vs using a third-party node — what changes in each threat model
- The **malicious node** threat: what a node can do (censor, provide stale state, hide notes from you) vs what it cannot do (forge proofs, steal funds, break privacy of correctly-encrypted notes)
- Comparison with browser trust model: PXE as a "private blockchain browser"

## Prerequisites

- [PXE Internals](../03-keys-accounts-privacy/05-pxe-internals.md)
- [Private Kernel Circuits](./03-private-kernel-circuits.md) — the circuits that enforce the oracle constraints

## Related Topics

- [Key Hierarchy](../03-keys-accounts-privacy/01-key-hierarchy.md) — master keys never leave the PXE
- [Note Encryption](../03-keys-accounts-privacy/02-note-encryption.md) — why notes cannot be decrypted by anyone but the owner
