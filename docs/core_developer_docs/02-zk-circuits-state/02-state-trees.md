---
title: The Five State Trees
description: Aztec's five Poseidon2 Merkle trees — Note Hash, Nullifier, Public Data, L1-to-L2 Message, and Archive — their types, heights, storage, and how circuits prove membership and non-membership.
sidebar_position: 2
tags: [internals, merkle-trees, state, nullifier, note-hash]
---

# The Five State Trees

:::note What you'll understand
How Aztec's five Merkle trees store all L2 state, the difference between append-only and indexed trees, how indexed trees prove non-membership via a sorted linked list (essential for double-spend prevention), and how `batchInsert()` optimizes bulk insertions. Prerequisites: [Proof System](./01-proof-system.md).
:::

## Why Merkle Trees?

Merkle trees let ZK circuits prove facts about large datasets using only O(log N) hashes:

- **Membership proof**: Given a leaf value + sibling path → compute root → assert equals expected root
- **Non-membership proof**: Show two adjacent leaves where `left.value < target < right.value` (indexed trees only)
- **State commitment**: The root hash commits to ALL leaves; any change → different root
- **Circuit-friendly**: ~42 Poseidon2 hashes to prove membership in a tree of 2⁴² leaves

## The Five Trees

```ts
// stdlib/src/trees/merkle_tree_id.ts
enum MerkleTreeId {
  NULLIFIER_TREE      = 0,  // Indexed (sorted linked list)
  NOTE_HASH_TREE      = 1,  // Append-only
  PUBLIC_DATA_TREE    = 2,  // Indexed (slot → value)
  L1_TO_L2_MESSAGE_TREE = 3, // Append-only
  ARCHIVE             = 4,  // Append-only (block headers)
}
```

| Tree | Type | Height | Leaf Content |
|------|------|--------|-------------|
| **Note Hash** | Append-only | 42 | Siloed note commitments |
| **Nullifier** | Indexed | 42 | Nullifier values + linked-list pointers |
| **Public Data** | Indexed | 40 | (slot, value) pairs |
| **L1→L2 Message** | Append-only | 36 | Message hashes |
| **Archive** | Append-only | 30 | Block header hashes |

## Append-Only vs Indexed Trees

### Append-Only (Note Hash, Archive, L1→L2 Message)

New leaves are always appended at `nextAvailableLeafIndex`:

```ts
// stdlib/src/trees/append_only_tree_snapshot.ts
class AppendOnlyTreeSnapshot {
  root: Fr;                       // Tree root
  nextAvailableLeafIndex: UInt32; // Monotonically increasing
}
```

The `nextAvailableLeafIndex` is part of the proof system — a circuit receives it as input and verifies that new leaves are appended at exactly this index. This ensures the append-only property is enforced by the circuit, not just the database.

### Indexed Trees (Nullifier, Public Data)

Indexed trees maintain a **sorted linked list** of values inside the tree:

```
Leaf structure: { value: Fr, nextValue: Fr, nextIndex: u32 }

Linked list (sorted by value):
[0 → ∞] initially (sentinel leaves)
After inserting 12, 5, 27:
[0] → [5] → [12] → [27] → [∞]
```

This enables **non-membership proofs**: to prove value 18 is NOT in the tree, show leaves `[12]` and `[27]` where `12 < 18 < 27`.

## Indexed Tree Insertion — The Low Leaf Mechanism

Inserting a new nullifier requires a **two-step Merkle update**:

```
Before: [5] → [12] → [27] → [∞]
Insert 18:

Step 1: Find "low leaf" = [12] (predecessor: 12 < 18 < 27)
        Update low leaf: nextValue: 27→18, nextIndex→new_index
        → Compute intermediate root

Step 2: Append new leaf [18] at nextAvailableLeafIndex
        Set: value=18, nextValue=27, nextIndex=old_27_index
        → Compute final root
```

**In-circuit verification** (performed by `rollup-tx-base-private`):
1. Prove low leaf membership against `start_root` → proves non-membership of 18
2. Update low leaf → compute `intermediate_root`
3. Append new leaf at `nextAvailableLeafIndex` → compute `end_root`
4. Output `end_root` as new tree state

Reference: `noir-projects/noir-protocol-circuits/crates/types/src/merkle_tree/indexed_tree/`

## Note Hash Tree — Private State

When a private function creates a note, only its **siloed hash** goes on-chain:

```
noteHash = poseidon2(value, owner, randomness)
siloedHash = poseidon2(contractAddress, noteHash)  // prevents cross-contract collisions
uniqueHash = poseidon2(nonce, siloedHash)           // nonce = hash(first_nullifier, index)
```

The owner stores the **preimage** (value, owner, randomness) locally in PXE's NoteStore. To prove they own and can spend a note:
1. Re-derive the note hash from the preimage
2. Prove the siloed+unique hash is in the Note Hash Tree (membership proof via sibling path)

## Nullifier Tree — Double-Spend Prevention

Spending a note emits a deterministic nullifier:

```
nullifier = poseidon2(noteHash, nsk_app)   // nsk_app = app-siloed nullifier key
```

The TX Base rollup circuit:
1. Proves the nullifier is NOT yet in the tree (non-membership proof)
2. Inserts the nullifier into the tree

The same note always produces the same nullifier. If it was spent before, the non-membership proof fails — the proof is invalid and the TX is rejected.

## Public Data Tree — Contract Storage

Public state is stored as `(storageSlot, value)` pairs:

```ts
class PublicDataTreeLeaf {
  slot: Fr;   // Contract-siloed storage key
  value: Fr;  // Current value
}
```

The AVM reads and writes this tree during public execution. Indexed tree semantics allow updating existing slots (not just appending).

## LMDB Storage and B-Tree Indexing

The trees are stored in **LMDB** (Lightning Memory-mapped Database) via the `kv-store` package. For indexed trees, a B-tree index maps `value → leafIndex` to support O(log N) low-leaf lookup:

```ts
// world-state/src/native/native_world_state.ts
// The native (C++) world state implementation manages LMDB directly
// TypeScript accesses it via native bindings
```

## batchInsert() Optimization

When multiple TX effects are inserted in one block, `batchInsert()` amortizes Merkle path computation:

Instead of computing N separate Merkle paths (one per insertion), `batchInsert()` computes a **subtree insertion**: if N leaves share a common subtree root, only the subtree root hash needs to propagate to the global root once. For a block of 64 TXs × 64 nullifiers = 4096 insertions, this reduces root computations by ~6× (subtree height 12 vs 42).

## Implementation Notes

- **Tree heights are fixed at deploy time**. A tree of height 42 has 2⁴² ≈ 4.4 trillion leaf slots. This is intentionally over-provisioned — the tree can never "fill up" in practice.
- **Archive tree (h=30)**: Stores 2³⁰ ≈ 1 billion block headers. At one block per slot and typical slot durations, this represents millennia of blocks.
- **Sibling paths as witnesses**: Circuits receive sibling paths as private inputs. They don't access the database directly — the PXE or orchestrator provides the paths as hints, and the circuit verifies them via hash recomputation.

## What Comes Next

The kernel circuits are what insert into these trees — [Private Kernel Circuits](./03-private-kernel-circuits.md).
