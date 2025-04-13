# 1. Introduction

## 1.1. Nomenclature

This document introduces a decentralized, crypto-economic protocol representing a major revision of the Polkadot Network. Originally proposed in RFC-31 under the name *CoreJam*, the finalized version is called *Jam*, a complete blockchain protocol based on the collect/refine/join/accumulate model of computation.

This protocol, **Jam**, combines ideas from both **Ethereum** and **Polkadot**:

- From **Polkadot**, it inherits shared security and validator-based crypto-economics.
- From **Ethereum**, it adopts a unified, composable computation model like the EVM.

Jam blends these to create a scalable yet coherent execution environment.

### In-Core vs On-Chain

- **In-Core**: Computation inside the validator’s runtime; fast but not always globally visible.
- **On-Chain**: State and logic stored on the blockchain; verifiable and shared by all.

Jam aims to balance in-core performance with on-chain trust and coherence.

## 1.2. Driving Factors

The primary objective in Web3 is **resilience**. A system should uphold its service guarantees regardless of adversarial economic actors. This requires the system to be effectively **unstoppable**.

Historical systems like Bitcoin and Ethereum introduced:
- **Bitcoin**: resilient but limited in general-purpose computation.
- **Ethereum**: more flexible with Turing-complete computation (bounded by gas).

Jam identifies five core goals:
1. **Resilience** – Unstoppable, censorship-resistant.
2. **Generality** – Capable of Turing-complete computation.
3. **Performance** – Fast and cost-efficient.
4. **Coherency** – Strong causal relationships across system state.
5. **Accessibility** – Low barriers to innovation and participation.

Of note is the **size-coherency antagonism**: performance and coherency often conflict in large systems.

## 1.3. Scaling under Size-Coherency Antagonism

The *size-coherency antagonism* posits that as systems grow in state-space, they lose coherence due to physical and computational latency. 

The typical solution—fragmenting systems into smaller, causally-independent subsystems—is seen in Polkadot, Cosmos, and Ethereum scaling strategies (e.g., shards, rollups).

Jam proposes a middle-ground:
- A new computation model combining a **mostly-coherent scalable layer** with a **fully-coherent synchronous layer**.
- Inspired by cache-affinity models in multi-CPU systems.
- Avoids full fragmentation and costly SNARK-based models, using crypto-economic incentives instead.
