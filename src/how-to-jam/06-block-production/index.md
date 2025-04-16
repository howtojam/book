# 6. Block Production and Chain Growth

Jam uses a hybrid consensus mechanism similar to Polkadot's BABE/GRANDPA. Its block production mechanism, called **Safrole**, ensures secure and orderly chain growth.

**Goals of Safrole**:

- **Limit block production rate** (one per 6-second timeslot).
- **Prevent forks** by ensuring only one validator can author a block per timeslot.
- **Maintain author anonymity** to reduce attack vectors.
- **Generate entropy** for use elsewhere in the protocol.

**Key Components**:

- **Sealing keys**: One per timeslot, derived anonymously from validators.
- **Validators**: Preselected and authorized participants.
- **State**: Safrole's state `γ` operates largely independently but interacts with:
  - `ι`: Incoming validator queue
  - `κ`: Active validator set
  - `τ`: Current timeslot
  - `η`: Entropy pool

**Cryptographic Design**:

- Uses **Bandersnatch Ring VRF**:
  - Allows anonymous validator selection.
  - Generates a deterministic, unbiasable **ticket**.
  - Tickets with best scores define sealing keys.

**Block Validation**:

Each block header must include:

- A **timeslot index** (`H_t`)
- A **seal signature** (`H_s`) tied to a validator's sealing key for that slot

This system ensures only one validator can produce a block at a given time, while maintaining security and decentralization.

## 6.1. Timekeeping

- The current block's **timeslot index** is tracked in state as `τ`.
- When processing a new block, the next timeslot `τ'` is taken directly from the block header (`H_t`).

```math
τ' ≡ H_t
```

- This index helps:
  - Detect **epoch changes**
  - Identify the **position** of a block within an epoch
- Epoch index `e` and slot phase index within epoch `m` are computed as:

```math
e R m = τ / E
```

```math
e' R m' = τ' / E
```

Where E is the number of slots per epoch.