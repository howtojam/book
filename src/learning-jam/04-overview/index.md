# 4. Overview

Block-level state-transition function:

`σ′ ≡ Υ(σ, B)`

- `σ⁰`: genesis state
- `σ′`: posterior state
- `σ`: previous state
- `Υ`: block-level state-transition function

## 4.1. The Block

Block:

`B ≡ (H, E)`

- `B`: Block
- `H`: Header
- `E`: Extrinsics

Extrinsic data (`E`):

`E ≡ (E_T , E_D , E_P, E_A, E_G)`

- `E_T`: *Tickets.* Used for the selection of validators
- `E_D`: *Disputes*. Validator dispute data
- `E_P`: *Preimages*. Requested static data
- `E_A`: *Availability*. Input data availability confirmations
- `E_G`: *Reports*. Validated workload reports guaranteed by validators

## 4.2. The state

The global state `σ` is composed of several independent segments, enabling parallel computation. It is formally defined as:

`σ ≡ (α, β, γ, δ, η, ι, κ, λ, ρ, τ, φ, χ, ψ, π, ϑ, ξ)`

Each segment has a specific purpose:

- `α`, `φ`: Core authorization rules and queues.
- `β`: Recent block metadata.
- `γ`: Other state relate to the validator key selection logic.
- `δ`: Services (equivalent to "accounts" in the Ethereum Yellow Paper).
- `η`: On-chain entropy pool.
- `ι`, `κ`, `λ`, : Validators — enqueued, currently active, and archived.
- `ρ`: Assigned core reports and their availability.
- `τ`: Timeslot index (based on Jam Common Era).
- `χ`: Privileged service identities.
- `ψ`: Judgments from validators.
- `π`: Validator performance statistics.
- `ϑ`, `ξ`: Work reports prepared for or already accumulated.

### 4.2.1. State Transition Dependency Graph

The state transition function `Υ` defines how each segment of the new state (`σ'`) is computed from the prior state and block. Dependencies between components are minimized to enable parallel execution.

Key dependency notations:

- `x' ≺ y` means `x'` depends on `y`.
- Daggered (`†`, `‡`) intermediate states help track multi-step derivations.
- Merge points (e.g. for preimages and availability) indicate synchronization barriers.

This design aids efficient implementation by explicitly exposing what can be computed in parallel and what must be synchronized.

```text
τ′ ≺ H
β_† ≺ (H, β)
γ′ ≺ (H, τ, E_τ , γ, ι, η′, κ′, ψ′)
η′ ≺ (H, τ, η)
κ′ ≺ (H, τ, κ, γ)
λ′ ≺ (H, τ, λ, κ)
ψ′ ≺ (E_D , ψ)
ρ† ≺ (E_D , ρ)
ρ‡ ≺ (E_A, ρ^†)
ρ′ ≺ (E_G, ρ^‡, κ, τ ′)
W^* ≺ (E_A, ρ′)
(ϑ′, ξ′, δ^‡, χ′, ι′, φ′, C) ≺ (W^*, ϑ, ξ, δ, χ, ι, φ, τ, τ ′)
β′ ≺ (H, E_G, β^†, C)
δ′ ≺ (E_P , δ^‡, τ ′)
α′ ≺ (H, E_G, φ′, α)
π′ ≺ (E_G, E_P , E_A, E_T , τ, κ′, π, H)
```

## 4.3. Which History?

The blockchain is a chain of blocks, each pointing to a previous one, starting from a known genesis block.

A block’s **state** is deterministically derived from its parent, forming a **canonical state**. The block with the most ancestors is called the **head**.

### Forks

Forks may occur when two valid blocks share the same parent. To manage this, the protocol ensures:

1. Forks are rare.
2. Forks are quickly resolved.
3. Recent blocks can be finalized as permanent history.

### Consensus

- **Safrole**: handles block production and reduces forks.
- **Grandpa**: finalizes blocks and secures history.

Invalid blocks must not be finalized. Grandpa ensures only valid chains persist.

## 4.4. Time

Jam assumes a shared notion of time (e.g. via NTP), unlike Polkadot.

Blocks are **invalid** if their timeslot is in the future.

Time is measured in **seconds since the Jam Common Era**, starting at `12:00 UTC on January 1, 2025`.

This timestamp is denoted as `𝓣`.

## 4.5. Best Block

The **best block** is the one most likely to remain in the canonical chain.

- **Grandpa** provides a highly reliable final block but may lag by 1–2 blocks.
- For low-latency needs (e.g. block authorship or state queries), we may prefer the **head of the best chain**, even if not finalized.

This trade-off balances **certainty vs. recency**, depending on the use case.

## 4.6. Economics

Jam defines a native **token** used to express economic incentives.

- A **balance** is an amount of tokens:  
  `ℕ_B ≡ ℕ_{2^64}` (i.e. max ~18 billion tokens)
- Tokens are divisible into units of `10^-9`.

Jam assumes several fixed prices (in tokens):

- `𝔅_I`: Minimum balance per mapping item  
- `𝔅_L`: Minimum balance per octet in a mapping  
- `𝔅_S`: Minimum balance for a service

Note: Jam uses a different denomination than Ethereum, Polkadot, or Kusama.

## 4.7. Virtual Machine and Gas

Jam introduces the **Polka Virtual Machine (PVM)** — a simplified, blockchain-focused version of the RISC-V `RV64EM` instruction set.

### Key Characteristics

- Inspired by RISC-V: compatible with existing tooling (e.g. LLVM, Rust, C++).
- Simpler than Ethereum’s EVM: no cryptographic or environmental opcodes.
- 13 general-purpose 64-bit registers.
- Paged memory: 32-bit addressable, 4KB pages marked as read/write/none.

### Gas

- Computation is bounded by **gas** (64-bit unsigned integer).
- Execution function `Ψ(...)` ensures runtime is proportional to gas consumed.
- Program may halt due to:
  - `halt`: normal termination
  - `panic`: runtime error
  - `oog`: out-of-gas
  - `fault`: invalid memory access
  - `host`: external call requested

PVM allows efficient and safe off-chain computation with deterministic outcomes usable in consensus.

## 4.8. Epochs and Slots

Jam uses a **proof-of-authority** consensus model, with validators selected via a staking mechanism (not detailed here).

Time is divided into:

- **Epochs**: fixed periods of 1 hour
- **Slots**: each epoch contains 600 slots, each lasting **6 seconds**

This setup is managed by the **Safrole** mechanism, which minimizes forks by tightly controlling block production timing.

- Slot index (`ℕ_T`) counts 6-second intervals since the **Jam Common Era**
- `ℕ_T` uses 32-bit unsigned integers, allowing protocol operation until ~year 2840

## 4.9. Core Model and Services

Jam introduces a more scalable alternative to Ethereum’s on-chain model by separating execution into two modes:

### In-core Consensus

- Only a **subset of validators** executes a given workload.
- Allows **parallel computation**, enabling ~300x more throughput than single-node models.
- Ensures correctness and availability through a 3-stage crypto-economic game:
  - **Guaranteeing** (economic stake)
  - **Assuring** (data availability)
  - **Auditing/Judging** (optional, for verification)

Execution is **stateless** and tied to a finalized block called the **lookup-anchor**.

### Service Accounts

- All accounts in Jam are **service accounts** with associated code, state, and balance.
- Each service has two code entry points:
  - **Refinement**: fast, off-chain (in-core), for preprocessing input data.
  - **Accumulation**: slow, on-chain, for executing stateful logic like balance transfers.

### Payment and Coretime

- Jam replaces Ethereum’s gas model with **coretime**:
  - A resource pre-purchased and assigned to an **authorization agent**.
  - Decouples blockspace purchase from identity (no "transactor" needed).

This model enhances scalability and flexibility while maintaining economic security.
