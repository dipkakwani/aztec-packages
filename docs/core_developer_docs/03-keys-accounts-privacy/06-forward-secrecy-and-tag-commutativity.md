---
title: Forward Secrecy and Tag Commutativity
description: Why ephemeral keys give per-note forward secrecy, the ECDH commutativity property that lets both sender and recipient independently compute note tags, and why this design avoids any on-chain address-to-note linkage.
sidebar_position: 6
tags: [internals, forward-secrecy, ecdh, tags, note-discovery, commutativity]
---

# Forward Secrecy and Tag Commutativity

:::caution Coming Soon
This page is a planned addition to the core developer documentation.
:::

## What This Page Will Cover

- **Why forward secrecy matters for notes**: unlike Signal (per-session), Aztec achieves **per-note** forward secrecy because each note uses a fresh ephemeral keypair
- Mathematical proof of ECDH forward secrecy: compromising `ivsk_m` today does not decrypt past notes because the ephemeral keys `esk` were discarded after sending
- **The ECDH commutativity property** in depth:
  - Sender computes: `tag = poseidon2(tsk_sender * ivpk_recipient, ...)`
  - Recipient computes: `tag = poseidon2(tsk_recipient * ivpk_sender, ...)` (same value!)
  - Why: `tsk_sender * ivpk_recipient = tsk_sender * (ivsk_recipient * G) = ivsk_recipient * (tsk_sender * G) = ivsk_recipient * tpk_sender`
- Why this eliminates the need for sender-recipient pre-coordination: no DH key exchange protocol needed
- **Privacy of the tagging scheme**: the tag reveals nothing to a third party (requires either `tsk_sender` or `ivsk_recipient` to verify)
- **Third-party linking**: without the tagging key, an observer cannot even tell which logs belong to the same sender-recipient pair
- The tag index / sender index: why tags are indexed per sender-recipient pair and how this prevents cross-pair correlation
- Comparison with alternative schemes: trial decryption (too slow), address-indexed logs (leaks receiver), sender-indexed logs (leaks sender)

## Prerequisites

- [Note Encryption](./02-note-encryption.md) — the full encryption pipeline and where tags fit in
- [Key Hierarchy](./01-key-hierarchy.md) — the `tsk_m` (tagging key) and `ivsk_m` (incoming viewing key)

## Related Topics

- [PXE Internals — LogService](./05-pxe-internals.md) — how the PXE uses tag commutativity in `fetchTaggedLogs()`
