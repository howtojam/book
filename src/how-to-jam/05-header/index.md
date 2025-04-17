# 5. The Header (H)

## Overview

The block header in JAM (Join-Accumulate Machine) is a compact but crucial structure, serving as the cryptographic anchor point for the blockchain's state, history, and consensus logic. It links blocks, captures vital state references, and acts as the foundation for consensus, identity, and integrity in JAM. The header is fixed and known before anything else happens. So, all the different parts of the protocol that work together to process a block can rely on it being present and unchanged.

This chapter explores the JAM header from the top down, explaining its purpose, structure, fields, and deeper implications. We'll begin with a plain introduction, then dive into a detailed technical breakdown of each component, its use in consensus, and its interaction with the rest of the system.

## What Is a Header?

A header is metadata that summarizes a block. It doesn't contain the transactions or computation results, but rather a set of cryptographically secure commitments to them.

In JAM, the header has a dual role:

1. It links a block to its parent.

2. It proves that the block was produced correctly, at the right time, by a valid author, using the correct data.

You can think of the header as the fingerprint of a block. If any part of the block changes, its header would change as well.

## The JAM Header Specification

Formally, the JAM header is defined as:
```
H ≡ (Hp,Hr,Hx,Ht,He,Hw,Ho,Hi,Hv,Hs)
```
Where:
- **Hp**: Parent hash
- **Hr**: Prior state root
- **Hx**: Extrinsic hash
- **Ht**: Timeslot index
- **He**: Epoch marker (optional)
- **Hw**: Winning tickets (optional)
- **Ho**: Offenders list
- **Hi**: Block author index
- **Hv**: Entropy/VRF signature
- **Hs**: Block seal (signature)

## Plain Description

| Field | Description |
|-------|-------------|
| `Hp`  | Hash of the parent block's header |
| `Hr`  | Merkle root of the state *before* applying this block |
| `Hx`  | Hash of the block's extrinsic data (inputs) |
| `Ht`  | Index of the time slot in which this block is valid |
| `He`  | Marker for the next epoch, includes randomness and validator set info |
| `Hw`  | List of VRF-winning tickets for upcoming validators |
| `Ho`  | List of misbehaving validator keys |
| `Hi`  | Index into the validator set of the block's author |
| `Hv`  | Entropy signature (verifiable random function output) |
| `Hs`  | Cryptographic seal signed by the author |

## Why It Matters

- **Immutable History**: The parent hash (`Hp`) links blocks together to form the chain.
- **Consensus Input**: Validators check `Ht`, `Hv`, `Hs`, and `Hi` to confirm authorship and legitimacy.
- **Efficient State Transition**: The prior state root `Hr` helps optimize execution.
- **Security**: The header is signed (`Hs`) and includes VRF randomness (`Hv`) to prevent manipulation.

## Deep Dive into Each Component

### Parent Hash (`Hp`)
Links to the previous block by its header hash. Ensures immutability and enables chain reconstitution.

### Prior State Root (`Hr`)
Merkle root of the blockchain state *before* this block. Unlike Ethereum, which uses posterior state roots, JAM does this to support pipelined block execution.

### Extrinsic Hash (`Hx`)
A digest of all the block's external inputs (transactions, messages, etc). Used for validation and deduplication.

### Timeslot Index (`Ht`)
Specifies the time slot when the block is *valid*. Must be greater than its parent's. Ensures the block is not from the future.

### Epoch Marker (`He`)
Only included in the first block of a new epoch. Contains:
- Entropy from the previous epoch
- The new validator set (Bandersnatch keys)

Useful for light clients and syncing nodes.

### Winning Tickets (`Hw`)
A list of tickets (VRF outputs) used to elect validators for future slots. Only present in some blocks.

### Offenders List (`Ho`)
List of validators identified as malicious or faulty in the previous epoch. Used to rotate them out.

### Author Index (`Hi`)
An index pointing to the validator key that authored the block. This is not the key itself, but a reference into a shared validator set.

### VRF Entropy Signature (`Hv`)
Verifiable random function output proving the block was authored anonymously by someone eligible. Powers randomness throughout JAM.

### Block Seal (`Hs`)
A cryptographic signature proving the block was authored by the entity represented in `Hi` using the key for the slot defined by `Ht`.

## Serialization

Headers are serialized into a byte sequence for network transmission and hashing. This process converts the structured header fields into a linear format (a sequence of bytes) so they can be:

- Transmitted over the network between nodes,
- Hashed deterministically for verification,
- Digitally signed for consensus.

The Two Forms of Serialization
There are two standard serialized forms for JAM headers:

#### `E(H)` → Full Header Encoding
This is the complete encoding of the header, including the cryptographic seal field Hs.

- It is used when a node wants to broadcast or store the full block header.
- It ensures the signature is part of the data, which is required when validating the header's authenticity from scratch.
- Used in final storage and archival.

#### `EU(H)` → Unsigned Header Encoding
This is the same header but without the seal (`Hs`). It includes all fields except the final signature.

- It's primarily used for signature verification:
    - To verify a header's signature, a node hashes `EU(H)` and checks if Hs is a valid signature over that hash.

- This separation avoids a circular dependency (you can't include the signature in the thing you're signing).
- This is akin to a "message payload" — it's the part the signer actually committed to.

## Functions & Equations Involving the Header

### Header Ancestry
```
Hp ∈ H, Hp ≡ H(E(P(H)))
```
- Defines the parent as the hash of the serialized parent header.

### Ancestor Chain
```
h ∈ A ⇔ h = H ∨ (∃i ∈ A : h = P(i))
```
- Defines a block's ancestors recursively.

### Extrinsic Hash Derivation
```
Hx ∈ H, Hx ≡ H(E(E))
```
- Used to check the integrity of extrinsic data.

### Timeslot Validation
```
Ht ∈ ℕ_T, P(H)t < Ht ∧ Ht ⋅ P ≤ T
```
- A block is only valid if its time-slot is in the past.

### State Merklization
```
Hr ∈ H, Hr ≡ Mσ(σ)
```
- Hr is derived from a state Merkle trie of σ (the prior state).

### Author Identity
```
Hi ∈ ℕ_V, Ha ≡ κ'[Hi]
```
- Maps an index to a validator's Bandersnatch key in the current validator set.

## Do's and Don't(s) of Working with Headers

✅ Do:

- Ensure the header is finalized before assuming its validity.
- Validate `Ht` to avoid blocks from the future.
- Store recent headers (e.g., last 24 hours) for ancestor validation.
- Use `EU(H)` for verifying seal signatures.
- Double-check `Hx` if validating extrinsics.

❌ Don't:

- Trust headers with incomplete or future-dated fields.
- Discard headers just because `He`, `Hw`, or `Ho` are empty — they are optional.
- Assume `Hs` alone proves authorship without verifying `Hv` and `Hi`.

## Summary

Headers in JAM are dense objects packing essential metadata about time, authorship, ancestry, randomness, and intent. They anchor each block in the blockchain, enforce consensus rules, and pave the way for scalable computation. Understanding how to interpret, validate, and use headers is foundational to JAM development and validator operation.
