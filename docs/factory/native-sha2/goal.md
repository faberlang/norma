# Goal: Native Faber SHA-2 Digests

**Status**: ready for factory — independent goal-check READY 2026-07-18
**Created**: 2026-07-18
**Target repo**: `/Users/ianzepp/work/faberlang/norma`
**Factory artifact dir**: `docs/factory/native-sha2/`
**Campaign**: [`native-faber-crypta`](../native-faber-crypta/CAMPAIGN.md)
**Prerequisite**: [`modular-word-width-family`](../../../../radix/docs/factory/modular-word-width-family/goal.md)
**Supersedes**: `docs/factory/native-sha256-receipts/goal.md`
**Primary surface**: `norma:crypta.digere`

## Summary

Implement SHA-256, SHA-384, and SHA-512 completely in native Faber under the
public Norma source library. `norma:crypta.digere` must hash arbitrary byte
inputs through Faber-owned padding, scheduling, compression, state
accumulation, and big-endian digest serialization. Generated Rust is a build
artifact, never the authored algorithm or standard-library source of truth.

This goal consumes Radix's complete unsigned modular word family:
`modulus<u32>` for SHA-256 and `modulus<u64>` for SHA-384/SHA-512.

## Core Invariant

If a deterministic standard-library algorithm can be expressed safely in
Faber, Norma owns its Faber implementation. A missing language guarantee is a
prerequisite to improve Faber, not permission to implement the algorithm in
Rust, Go, TypeScript, Python, Bash, a codegen template, or a host provider.

## Problem

- `norma/src/crypta.fab` currently exposes `digere` as a fatal `mori` deferral.
- Its comments name SHA-256, SHA-384, and SHA-512 without implementing any of
  them.
- The predecessor SHA-256 goal correctly selected native Faber ownership but
  stopped before the newly approved all-width modular family and therefore
  deferred SHA-384 and SHA-512.
- A compiler compression-round fixture proves arithmetic mechanics but does not
  ship a standard-library digest implementation.
- A target-language or provider implementation would duplicate Norma by
  backend and defeat the purpose of the modular-word language work.

## Public Contract

The deferred stub is replaced by this clean-break signature:

```fab
functio digere(textus alg, octeti data) → octeti ⇥ textus
```

- `alg` accepts exactly `sha256`, `sha384`, and `sha512` in lowercase.
- Unknown, empty, mixed-case, and aliased names fail with a stable text error.
- `data` is processed as bytes, including embedded zero bytes.
- Inputs over 64 MiB fail before padding allocation or compression.
- Results are raw digest bytes: 32 for SHA-256, 48 for SHA-384, and 64 for
  SHA-512, in standard big-endian order.
- `digere` does not hex-encode, base64-encode, read files, or acquire host
  capabilities.

The uniform 64 MiB limit is an initial resource contract, not an algorithmic
SHA-2 limit. It keeps byte lengths and padding allocation bounded while the
library lacks streaming digest handles.

Public failure text is exact:

- Every invalid algorithm spelling fails with
  `norma:crypta.digere: algorithm must be sha256, sha384, or sha512`.
- An oversized input fails with
  `norma:crypta.digere: input exceeds 67108864-byte limit`.
- Exactly 67,108,864 input bytes are admitted. 67,108,865 bytes are rejected
  before padding allocation.

## Goals

- Implement complete SHA-256 padding, 64-word schedule, 64-round compression,
  state accumulation, and 32-byte serialization in Faber.
- Implement one shared SHA-512-family core in Faber: 80-word schedule,
  80-round compression, and 64-bit state accumulation.
- Implement SHA-384 by selecting its specified initial state and truncating the
  SHA-512-family result to 48 bytes. Do not duplicate the 80-round core.
- Use `modulus<u32>` and `modulus<u64>` throughout compression state, schedule,
  constants, bitwise expressions, and accumulated state.
- Compose rotations from shifts and bitwise OR until a separate rotate surface
  is accepted.
- Keep the public functions and true `@ privata` SHA engines/helpers together
  in `src/crypta.fab`. Current package interfaces make every `src/**/*.fab`
  module directly importable, so separate nested engine files would create
  unintended public surfaces.
- Add authoritative NIST byte-oriented known-answer vectors for empty input,
  `abc`, padding boundaries, binary data, and multi-block messages.
- Prove the same Norma source through ordinary `faber run`, generated Rust
  debug/release execution, and MIR/reference execution where the target admits
  the required standard-library source.

## Non-goals

- SHA-1, SHA-224, SHA-512/224, SHA-512/256, SHA-3, Blake2, or MD5.
- HMAC; it is the next campaign stage after the digest API and secret-key
  prerequisites are accepted.
- AES, Ed25519, Argon2, password hashing, signatures, encryption, key
  generation, or entropy.
- RustCrypto or another foreign cryptographic dependency.
- A `crypta:digere` host route, `ad` call, codegen template, shell/process
  adapter, Python adapter, or application-local implementation.
- Streaming digest handles.
- Hexadecimal rendering inside `digere`.
- FIPS module validation, constant-time claims, or a general secret-memory
  claim. SHA-2 digest input is not treated as secret key material by this goal.

## Ground Truth Researched

- `src/crypta.fab`: `digere` and every other cryptographic function remain
  `mori` deferrals; the comments claim the SHA-2 names.
- `AGENTS.md` and `README.md`: Norma's public source of truth is `.fab` under
  `src/`; `@ externa` and `@ subsidia` are prohibited.
- `src/caelum.fab` and `src/caelum/*.fab`: nested source modules are directly
  importable public interface paths, so they are not a privacy boundary.
- `../examples/corpus/octeti/unify.fab`: `octeti` is representation-identical
  to `lista<numerus<u8>>` and receives list access/mutation methods.
- `radix/docs/factory/modular-word-type/delivery-32-bit.md`: the shipped
  `modulus<u32>` contract is ship-clear.
- `radix/docs/factory/modular-word-width-family/goal.md`: full-range unsigned
  values and `modulus<u64>` are the required language substrate.
- NIST FIPS 180-4 specifies SHA-256, SHA-384, and SHA-512, including padding,
  schedules, constants, functions, and digest construction:
  `https://doi.org/10.6028/NIST.FIPS.180-4`.
- NIST CAVP publishes byte-oriented SHA response vectors for informal
  implementation verification:
  `https://csrc.nist.gov/Projects/Cryptographic-Algorithm-Validation-Program/Secure-Hashing`.
- Operator decision, 2026-07-18: the purpose of the modular-word work is a
  Norma Crypta library whose algorithms are completely authored in Faber.

## Reference Packet

Before implementation, inspect:

- `src/crypta.fab`, `AGENTS.md`, and `README.md`;
- `src/caelum.fab` and `src/caelum/*.fab` for evidence that nested files are
  importable interfaces rather than private implementation units;
- `../../../../radix/docs/factory/modular-word-width-family/{goal,delivery}.md`;
- `../../../../radix/docs/factory/host-gateway-coverage/goal-d3-crypta.md` for
  the broader approved Crypta ownership and resource profile;
- `../../../../examples/corpus/octeti/unify.fab` and modular-word exempla;
- `../../../../faber/src/commands/run.rs` and package build/run wiring;
- NIST FIPS 180-4 and NIST CAVP SHA byte-oriented response vectors.

## Constraints And Invariants

- Algorithm source lives only in Norma `.fab` files.
- All SHA engines and compression helpers remain `@ privata` declarations in
  `src/crypta.fab`; no helper module path is created.
- SHA-256 state, schedule, round constants, and working words retain
  `modulus<u32>` identity.
- SHA-384/SHA-512 state, schedule, round constants, and working words retain
  `modulus<u64>` identity, including constants above `i64::MAX`.
- Compression uses ordinary modular operators or their established method
  twins. It does not emulate wrapping by widening, manual post-operation masks,
  or generated-target assumptions.
- Padding appends `0x80`, zero fills to the algorithm block boundary, and writes
  the standard big-endian bit length: 64 bits for SHA-256 and 128 bits for the
  SHA-512 family. Under the 64 MiB input limit, the upper 64 bits of the latter
  are zero but must still be serialized.
- SHA-256 processes 512-bit blocks and 64 rounds. SHA-384/SHA-512 process
  1024-bit blocks and 80 rounds.
- SHA-384 and SHA-512 share scheduling and compression logic but use their
  specified distinct initial values and output lengths.
- Byte packing and digest serialization are explicitly big-endian.
- No input bytes appear in error text or diagnostics.
- A compiler failure is repaired in Radix. It is not worked around by moving
  an algorithm out of Faber.

## Architecture Direction

`src/crypta.fab` owns public algorithm dispatch, failure behavior, and all SHA-2
implementation helpers in one interface file:

```text
src/crypta.fab
  ├── public digere dispatch
  ├── @ privata 32-bit SHA-256 engine and helpers
  └── @ privata shared 64-bit SHA-384/SHA-512 engine and helpers
```

Private engines consume and return raw `octeti`; internal state uses fixed-size
lists of modular words because no new public digest-state type is required.
Tests exercise the public `digere` contract. They do not expose private helpers
for test convenience. If `crypta.fab` becomes too large, a separate accepted
module-visibility feature must land before splitting implementation files.

## Supporting Skills

- `faber`: write canonical Faber and validate package/module behavior.
- `factory`: execute each delivery-sized stage through verification and commit.
- `red-green`: land NIST vectors before algorithm bodies.
- `correctness`: audit padding, length encoding, schedule bounds, rotations,
  state accumulation, truncation, and byte order.
- `black-hat`: authorized adversarial API review after deterministic digest
  correctness, with no claim that digest code establishes secret-key safety.
- `cleanliness` and `polish`: keep facade and engines readable after the known
  implementation pattern is proven.

## Implementation Shape

- Accept the exact Radix modular-width commit and rerun its consumer gate.
- Change `digere` to the failable three-algorithm contract and land red NIST
  vectors through a package-shaped exemplum.
- Implement and accept the SHA-256 engine.
- Implement the shared SHA-512-family engine, then add SHA-384 configuration
  and truncation.
- Integrate public dispatch, error behavior, resource limits, and package proofs.
- Run cross-lane, debug/release, adversarial, documentation, and release-prep
  closeout.

## Release Posture

Decision: release preparation only; publication is deferred.

- This replaces a public fatal stub with a working failable API. Prepare Norma
  version/changelog and Faber product release notes.
- No Cista publication, version tag, or external release is authorized.

## Exit Strategy

Decision: clean pre-release rollback; no foreign fallback.

- If a digest implementation fails its vectors, keep that algorithm explicitly
  unsupported while repairing Faber or Norma. Do not route it to a provider.
- Before public release, the entire native SHA-2 slice can be reverted with its
  API and exempla. After release, algorithm names and byte outputs are public
  contracts.

## Acceptance Criteria

- `norma:crypta.digere` contains a native Faber body and no `mori`, `ad`,
  codegen template, external process, or provider path for SHA-2.
- `sha256`, `sha384`, and `sha512` return exactly 32, 48, and 64 raw bytes.
- Empty, `abc`, embedded-zero, padding-boundary, and multi-block NIST vectors
  match byte-for-byte for all three algorithms.
- Unknown, empty, mixed-case, and aliased names fail through the failable
  contract with the exact algorithm error above.
- A 67,108,864-byte input reaches digest processing; a 67,108,865-byte input
  fails with the exact size error above before padding allocation.
- SHA-256 uses `modulus<u32>`. SHA-384/SHA-512 use `modulus<u64>` and include
  literal constants above `i64::MAX` without decomposition workarounds.
- SHA-384 shares the SHA-512-family compression engine and uses its specified
  IV and 48-byte truncation.
- The implementation contains no hand-authored Rust/Go/TypeScript/Python/Bash
  algorithm and no cryptographic provider dependency.
- A normal package under `exempla/crypta-sha2/` runs through `faber run` and
  proves the public vectors.
- Generated Rust debug and release executions agree. MIR/reference execution
  agrees wherever the package path is supported.
- `./scripta/check-source` confirms that Norma's public interface root remains
  `.fab`-only and contains no prohibited external/subsidia annotations.

## Validation

```bash
cd /Users/ianzepp/work/faberlang/norma
./scripta/check-source
./scripta/check-promoted-helper-imports
FABER_LIBRARY_HOME=.. timeout 300 cargo run --manifest-path ../faber/Cargo.toml -- check src/crypta.fab
FABER_LIBRARY_HOME=.. timeout 300 cargo run --manifest-path ../faber/Cargo.toml -- run exempla/crypta-sha2
```

From Radix:

```bash
timeout 300 cargo test -p radix modular_word
timeout 300 cargo test -p exempla --lib crypta
cargo build --manifest-path ../faber/Cargo.toml
./scripta/test
```

- Run the generated package in debug and release configurations.
- Pin the exact NIST CAVP response-vector archive and selected cases in the
  delivery ledger before implementation.
- Inspect generated Rust only as compiler evidence; never edit it.
- Require an independent correctness and adversarial review before acceptance.

## Open Questions

None block lowering. Delivery must pin the exact CAVP response files and record
whether MIR package execution can consume the full Norma module graph on the
implementation head; lack of MIR package support does not authorize a second
algorithm implementation.

## Stop Conditions

- Stop if the complete modular-width prerequisite is not accepted on current
  Radix main.
- Stop if any SHA path requires a foreign algorithm dependency, host route,
  shell/process adapter, codegen template, or generated-source edit.
- Stop and repair Radix if Faber cannot express exact padding, byte packing,
  modular expressions, list mutation, or full-range constants.
- Stop if SHA-384 duplicates rather than shares the SHA-512-family core.
- Stop if implementation creates directly importable helper modules under
  `src/crypta/` or exposes engine helpers as public API.
- Stop if tests use only self-generated expected values instead of pinned NIST
  vectors.
- Stop before release if public package, debug/release, vector, source-library,
  target-honesty, or independent-review evidence is incomplete.
