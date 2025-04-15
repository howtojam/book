# 3. Notational Conventions

## 3.1. Typography

- **Local terms**: Lowercase roman letters (`x`, `y`... for sets or sequences. `i`, `j`... for numerical indices). To refer to a value only relevant within some localized section of the document.
- **Booleans/Functions**: Capitalized roman letters (`A`, `F`).
- **Complex values**: Boldface.
- **Global sets**: Blackboard typeface (`ℕ`).
- **Imported functions**: Calligraphic (`𝓗` for Blacke2 hashing).
- **Internal functions**: Uppercase Greek (`𝚼` for state transition).
- **State values**: Lowercase Greek (e.g., `σ` for state identifier), possibly bold for abnormally compex values.

## 3.2. Functions and Operators

- **Precedes relation**: `y ≺ x` ⇔ ∃f such that `y = f(x)`. To indicate that one term is defined in terms of another.
- **Null coalescing**: `𝓤(a₀, ..., aₙ)` returns the first non-empty value (≠ ∅). If all values are empty (∅), then it returns ∅.

## 3.3. Sets

- **Power sets**: `𝒫(s)`; restricted: `𝒫₂({1,2,3})_2 = { {1,2}, {1,3}, {2,3} }`
- **Scalar application**: `{1,2,3} + 3 = {4,5,6}`
- **Apply function to all elements**: `f^#({1,2}) = {f(1), f(2)}`
- **Disjoint sets**: `A ⊥ B` means `A ∩ B = ∅`
- **Optional sets**: `A? = A ∪ {None}`
- **Invalid or unexpected value**: `∇`

## 3.4. Numbers

- `ℕ` – Natural numbers (incl. 0); `ℕₙ = {0, 1, ..., n-1}`
- `ℤ` – Integers; `ℤ_{a...b} = {a ..., b-1}`
- `ℕ_L` – set of lengths of octet sequences  (`ℕ_{2^32}`). Meaning all natural numbers less than 2³².
- Modulo: `5 % 3 = 2`; Quotient & remainder: `5 ÷ 3 = 1 R 2`

## 3.5. Dictionaries

- **Dictionary**: a possibly partial function. It maps **keys** to **values**.
  - Unlike regular functions, dictionaries are **enumerable** — we can list all their `(key → value)` pairs.
- A dictionary is written as: `𝔻 ⊂ { { (k ↦ v) } }`
- **Subscript access**: `d[k] = v` if key exists, otherwise `∅` 
- **Keys** of `d`: `𝒦(d) = { k | ∃v : (k ↦ v) ∈ d }`
- **Values** of `d`: `𝒱(d) = { v | ∃k : (k ↦ v) ∈ d }`. Duplicates are collapsed since `𝒱(d)` is a set.
- `d ∪ e` merges two dictionaries, where `e` takes priority on key collisions: `d ∪ e = (d \ 𝒦(e)) ∪ e`

## 3.6. Tuples

- Structured group of values: `(a ∈ ℕ, b ∈ ℕ)`
- Named access: `t_a`, `t_b`

## 3.7. Sequences

- **Sequence**: ordered list of elements from a set `T`, denoted as `⟦T⟧`
  - It maps from `ℕ → T` (partial or complete, depending on length)
- Access element: `i`: `s[i]` or `sᵢ`
- Access a range: `[0, 1, 2, 3]_...2 = [0, 1]`, `[0, 1, 2, 3]_1...+2 = [1, 2]`
- Sequence length: `|s|`
- Modulo index: `s[i]⟳ = s[i % |s|]`
- Final element: `last(s) = x` if `s = [..., x]`

### 3.7.1. Construction

- Sequences can be built using indexed values: `[x₀, x₁, ...]_n` → sequence of `n` elements, from `x₀` to `xₙ₋₁`
- Or using a function of the index: `[f(i) | i < ℕₙ] ≡ [f(0), f(1), ..., f(n-1)]`
- **Apply function to all elements**: `f^#(s)`  applies `f` to each item in sequence `s`
- Sequences can be sorted by key:  
  `[i_k | i ∈ X]` → sort `i` by the key `i_k`  
  `[i | i ∈ X]` → sort directly by the elements  
  `[i⧹⧹ | i ∈ X]` → sort and remove duplicates
- You can turn a sequence into a set: If `s = [1, 2, 3, 1]`, then `{a | a ∈ s} = {1, 2, 3}`
- Sequences are ordered like dictionary keys: `[1, 2, 3] < [1, 2, 4]`, `[1, 2, 3] < [1, 2, 3, 1]`

### 3.7.2. Editing

- `x ⌢ y`: Concatenates two sequences.
- `x ++ i`: Adds element `i` to the end of `x`.
- `→sⁿ`: First `n` elements of `s`.
- `←sⁿ`: Last `n` elements of `s`.
- `Tₓ`: Transposes a sequence of sequences (or tuples).
- `s ⊘ {v}`: Removes the first (left-most) `v` from `s`.

### 3.7.3. Boolean values

- `𝔹_s` is the set of Boolean strings of length `s`: `𝔹_s = ⟦{⊥, ⊤}⟧_s`
- Booleans map to bits: `⊤ = 1`, `⊥ = 0` → `𝔹 = ⟦ℕ₂⟧`
- `bits(𝒴)` gives the bit sequence of an octet string `𝒴`, with the most significant bit first.  
  Example: `bits([160, 0]) = [1, 0, 1, 0, 0, 0, 0, 0, ...]`
- The **not** operator applies to single or multiple booleans:  
  `¬⊤ = ⊥`  
  `¬[⊤, ⊥] = [⊥, ⊤]`

### 3.7.4. Octets and Blobs

- `Y` is the set of octet strings (a.k.a. "blobs") of any length.
- `Y_x` is the set of octet strings of length `x`.
- `Y_$` is the subset of ASCII-encoded strings.
- An **octet** (byte) maps to a natural number `< 256`, but they are not treated as the same:
  - Octets serialize as themselves.
  - Natural numbers may serialize to **multiple octets**, depending on size and encoding.

### 3.7.5. Shuffling

- `𝓕(s, entropy)` returns a permutation of `s` based on entropy input (Fisher-Yates algorithm).

## 3.8. Cryptography

### 3.8.1. Hashing

- `𝓗` denotes the set of 256-bit hash values (`𝓨₃₂`), with `𝓗⁰` defined as the zero hash `[0]₃₂`.
- Two cryptographic hash functions are used:
  - `𝓗(m)`: Blake2b-256
  - `𝓗_K(m)`: Keccak-256 (as in Ethereum)
- `𝓗ₓ(m)` returns the first `x` bytes of the Blake2b hash.
- Hash inputs are serialized via `𝓔`, and decoded with `𝓓`.
  - Example: `𝓔₄(x ∈ ℕ_{2³²}) ∈ 𝓨₄`, `𝓓₈(y ∈ 𝓨₈) ∈ ℕ_{2⁶⁴}`

### 3.8.2. Signing Schemes

#### Ed25519

- Signatures: `sigₖ(m) ∈ 𝓨₆₄`
- Requires secret key with public key `k ∈ 𝓨₃₂`
- Public key set: `𝓗_E`

#### BLS

- Public keys: `𝓨_{BLS} ⊂ 𝓨₁₄₄` (BLS12-381 curve)

#### Bandersnatch

- Public key set: `𝓗_B`
- Contextual signature: `𝓢ₖ(x, m) ∈ 𝓨₉₆`
- VRF proof: `𝓥(r, x, m) ∈ 𝓨₇₈₄` for `r ∈ 𝓨_R`
  - `𝓨_R ⊂ 𝓨₁₄₄` is the set of roots (each corresponding to a set of public keys)
  - `𝓞(s)` derives a root from a sequence `s ∈ [𝓗_B]`

Both signature and VRF:
- Prove secret key knowledge.
- VRF output: `𝓸𝓾𝓽(𝓥) ∈ 𝓗` is a high-entropy hash influenced by context `x`.

#### Signature Function

- Defined as: `𝓢ₖ(m) ∈ sigₖ(m) ∪ 𝓢ₖ([], m)`
- Abstracts over both Ed25519 and Bandersnatch
- Requires secret key knowledge
