# 5. The Header

Each block header `H` includes several fields:

- `H_p`: Hash of the **parent** block header.
- `H_r`: **Root hash** of the **prior** state (unlike Ethereum and Polkadot, which use the *post-state*).
- `H_x`: Merkle root of the block's **extrinsic data**, allowing proof of individual report inclusion.
- `H_t`: **Time-slot index** when the block was authored (must be strictly later than the parent).
- `H_e`, `H_w`, `H_o`: Optional **markers** for epoch fallback entropy, winning tickets, and validator offenders.
- `H_i`: Index of the **author validator** in the updated validator set.
- `H_v`, `H_s`: Bandersnatch VRF-based **entropy** and a **seal signature**.

All fields together form:

`H ≡ (H_p, H_r, H_x, H_t, H_e, H_w, H_o, H_i, H_v, H_s)`

Each block links cryptographically to its parent, forming a **chain** of headers.

## 5.1. The Markers

Three optional metadata fields in the header support consensus and security:

- `H_e` (Epoch Marker): Backup keys and entropy if epoch finalization fails.
- `H_w` (Winning Tickets): Sealing tickets for the next epoch's slots.
- `H_o` (Offenders): Ed25519 public keys of validators deemed to have misbehaved.

These support fallback consensus, validator accountability, and slot sealing in future epochs.
