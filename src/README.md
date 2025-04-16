# How To JAM

![Logo JAM](./img/logo-jam.png)

> **Polkadot Blockchain Academy Alumni - Lucerne**

A Deep Dive into the Join-Accumulate Machine (JAM) of Polkadot.

---

This book is a companion guide to the *Gray Paper* of the Join-Accumulate Machine (JAM), the next-generation core protocol of the Polkadot network.

Its goal is twofold:

1. To provide a detailed and accessible interpretation of the Gray Paper, clarifying the technical foundations and formal logic behind JAM.
2. To teach readers how to develop JAM programs in Rust, helping developers understand the execution model, data structures, and principles behind Polkadot 2.0.

Whether you're a researcher trying to make sense of JAM's formal semantics, or a developer aiming to build the next killer parachain, this book aims to bridge the theory and practice.

## Summary

This document introduces a decentralized, crypto-economic protocol—**Jam**—that represents a major revision of the Polkadot Network. The protocol takes inspiration from both **Ethereum** and **Polkadot**:

- From **Polkadot**, it inherits many cryptographic primitives and economic mechanisms, such as the validator game-theory system (*Elves*).
- From **Ethereum**, it draws the idea of a **single, shared, composable computation environment**, similar to Ethereum's EVM-based model.

Thus, the design of Jam can be seen as a **hybrid**, combining the scalability and shared-security features of Polkadot with the developer-friendly, general-purpose programmability of Ethereum.

The protocol distinguishes between two execution layers:

- **In-Core**: Refers to the logic that runs **inside the node's runtime (validator core)**. This includes the validator's own computation processes, often related to validating blocks or interpreting state transitions. It is not necessarily written in a universal language and may not be visible or verifiable on-chain.

- **On-Chain**: Refers to logic or data that is **recorded and verifiable on the blockchain itself**. This includes consensus-critical state transitions, final application state, and smart contract logic. It is persistent and can be checked by any node.

In Jam, the goal is to structure computation such that the **in-core processing** provides scale and performance, while the **on-chain record** retains coherence, verifiability, and trustlessness.

## Requirements

Building this book requires [mdBook](https://github.com/rust-lang/mdBook). To install it:

[mdBook]: https://github.com/rust-lang/mdBook

```bash
cargo install mdbook
```

### `mdbook` usage

To build the book locally, use the `build` sub-command:

```bash
mdbook build
```

The output will be placed in the `book` subdirectory. To check it out, open the `index.html` file in your web browser. You can pass the `--open` flag to `mdbook build` and it'll open the index page in your default browser (if the process is successful) just like with `cargo doc --open`:

```bash
mdbook build --open
```

There is also a `test` sub-command to test all code samples contained in the book:

```bash
mdbook test
```

## Contributing

How To JAM is a community-driven effort. If you find errors, unclear explanations, or have suggestions, we encourage you to open an issue or submit a pull request.

If your contribution is significant or structural, please open an issue beforehand so we can discuss the direction together.
