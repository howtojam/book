# 2. Previous Work and Present Trends

## 2.1. Polkadot

Jam reuses many of the crypto-economic and cryptographic techniques introduced in Polkadot, particularly the *Elves* protocol. However, Jam offers a service abstraction closer to actual computation as performed by incentivized validators.

Polkadot scales by distributing workloads across isolated parachains, connected via asynchronous message-passing (`XCMP`/`XCM`). This lowers composability compared to monolithic platforms like Ethereum.

Key limitations of Polkadot:
- Limited inter-chain composability.
- Accessibility barriers: launching a parachain requires winning a scarce auctioned slot.
- Ecosystem fragmentation leads to development silos.

## 2.2. Ethereum

Ethereum, originating from the [*Yellow Paper*](https://ethereum.github.io/yellowpaper/paper.pdf), introduced a flexible, general-purpose platform powered by the EVM. Over time, it has added extensibility (e.g., precompiles, new transaction formats), though performance remains largely similar to its launch in 2015.

It now plans to scale via:
- **Danksharding**: shared responsibility for data availability among validators.
- **Roll-ups**: off-chain computation models, often using zero-knowledge SNARKs for proof-of-computation.

Challenges:
- Centralization in roll-up ecosystems (e.g., Lido, Starkware).
- Roll-up heterogeneity introduces complexity in trust and composability.
- zk-SNARKs are extremely costly to compute and prove, often requiring specialized hardware and thousands of times the resources of native execution.

### 2.2.1. SNARK Roll-ups

Ethereum favors zk-SNARK-based roll-ups for scaling, but:
- SNARKs are costly and complex to generate.
- General-purpose computation is hard to express in SNARK-friendly formats.
- Proof generation is orders of magnitude slower and more expensive than native execution.
- Centralized roll-up operators can become single points of failure (e.g., Starkware).

While improvements are expected, SNARK-based approaches are still far from economically competitive with crypto-economic systems like Jam's Elves.

## 2.3. Fragmented Meta-Networks

Projects like **Cosmos** and **Avalanche** use a fragmentation approach:
- Independent validator sets per network.
- No shared security, leading to weaker trust guarantees.
- Interoperability is weak and composability suffers.

Mitigation attempts include:
- **Replicated Security**: every validator validates every network (inefficient).
- **Validator Partitioning**: as proposed by OmniLedger, rotating validator assignments across networks.

Jam's model uses non-redundant partitioning combined with proposal-auditing games and causal entanglement for high security and better scaling.

## 2.4. High-Performance Fully Synchronous Networks

Projects like **Solana** focus on high throughput by:
- Requiring powerful hardware (e.g., 512 GB RAM).
- Emphasizing synchronous execution and fast finality.

Drawbacks:
- Centralization via hardware requirements.
- System fragility: Solana has had multiple major outages.
- Poor historical data availability: Solana nodes upload data to Google Cloud.

Solana demonstrates the limits of synchronous scaling without decentralization trade-offs.

---

In summary, **Jam proposes a middle path**: retaining coherence and composability without resorting to costly zk-SNARKs or fragmented architectures, leveraging crypto-economic incentives and layered architecture for scalable Web3 applications.
