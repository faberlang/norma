# Goal: Native Faber SHA-256 Artifact Receipts

**Status**: forged; proposed; goal-check is `NOT READY` pending accepted
`modulus<u32>` corrective closeout
**Created**: 2026-07-18
**Revised**: 2026-07-18 — provider-backed SHA replaced by native Faber ownership
**Next consumer**: `goal-check`, then `delivery`/`factory` after the prerequisite
gate is green
**Target repos**: `/Users/ianzepp/work/faberlang/norma` and
`/Users/ianzepp/work/faberlang/radix`; the public `faber` CLI supplies the normal
package/run proof but does not own the algorithm
**Factory artifact dir**: `docs/factory/native-sha256-receipts/`
**Primary surface**: native `norma:crypta.digere("sha256", data)` and one
runnable package proof

## Summary

Implement SHA-256 once in native Faber as part of the public `norma:crypta`
standard library. Ordinary Faber applications must be able to hash artifact
bytes without shelling out, invoking Python, linking a RustCrypto provider, or
reimplementing SHA-256 in application code.

This is the first consumer of Radix's
[`modulus<u32>`](../../../../radix/docs/factory/modular-word-type/goal.md)
contract and the first Faber-native slice of the broader
[`Goal D3 - crypta`](../../../../radix/docs/factory/host-gateway-coverage/goal-d3-crypta.md).
It covers only `sha256`. SHA-384 and SHA-512 remain desired native Faber
algorithms, but they are deferred until `modulus<u64>` and exact full-range
unsigned 64-bit literals exist. They must not become permanent target-language
fallbacks merely because that language work is incomplete.

The immediate external consumer is Swarm Bootcamp, whose adapter claim remains
user-provided context. That repository is not present in this workspace and is
not modified by this goal.

## Core invariant

If a deterministic standard-library operation can be expressed safely in
Faber, Norma owns a Faber implementation. A language/compiler limitation is a
prerequisite to improve Faber, not a reason to duplicate the standard library
in Rust, Go, TypeScript, or another host language.

SHA-256 needs no operating-system capability. Its padding, schedule,
compression, and digest serialization therefore remain entirely in Faber.

## Problem

- `norma:crypta.digere` is currently a fatal `mori` deferral.
- Checked `numerus<u32>` arithmetic cannot implement SHA-256 because the
  compression function requires addition modulo `2^32`.
- Radix now has a dedicated `modulus<u32>` family and a one-round SHA fixture,
  but corrective review must prove complete generated-target behavior before a
  full standard-library algorithm depends on it.
- The current `digere` signature cannot report an unknown algorithm without a
  fatal path. Because no working implementation contract exists to preserve,
  this goal makes the function explicitly failable.
- Canonical lowercase hexadecimal rendering is not landed. Digest computation
  therefore returns raw `octeti`; hex remains a separate standard-library goal.

## Public contract

The first live signature is a clean break from the deferred stub:

```fab
functio digere(textus alg, octeti data) → octeti ⇥ textus
```

- `alg` accepts exactly lowercase `sha256` in this slice.
- Unknown, empty, mixed-case, or aliased names fail with a stable text error.
- `data` is hashed as bytes, including embedded `0x00`; no text conversion or
  C-string termination is allowed.
- Inputs over 64 MiB fail before padding allocation or compression.
- The result is exactly 32 digest bytes in SHA-256 big-endian order.
- Raw bytes remain raw bytes. Do not base64- or hex-encode the result inside
  `digere`.

The 64 MiB ceiling keeps the SHA-256 bit length below `2^32`. Padding still
writes the standard 64-bit big-endian length field, with its high 32 bits zero
under this v1 limit. Removing that limit requires a separate length-carrier and
resource-policy review.

## Goals

- Implement SHA-256 padding, message schedule, 64-round compression, state
  accumulation, and digest serialization in native Faber source owned by Norma.
- Use `modulus<u32>` for compression words and ordinary modular operators or
  their established method twins. Do not reproduce wrapping with widening,
  masks, target snippets, or generated-Rust assumptions.
- Compose rotate-right from the proven modular shift/bitwise operations until a
  separately accepted rotation intrinsic exists.
- Keep the public dispatch in `norma:crypta.digere`; private helpers may live in
  a focused Norma module such as `src/crypta/sha256.fab` if the delivery proves
  the current import/export convention.
- Add authoritative known-answer, padding-boundary, binary-byte, and multi-block
  vectors before the implementation is accepted.
- Prove the same Norma source through Faber round-trip, MIR/reference execution
  where supported, and generated Rust debug/release execution.
- Require unsupported emit targets to reject `modulus<u32>` explicitly. No
  target may erase the modular family or silently substitute native `int`.
- Add a package-shaped Norma exemplum under `exempla/crypta-sha256/` and run it
  through the ordinary `faber run` package path without a test-only provider.
- Keep lowercase hexadecimal rendering out of this goal; link the eventual
  native Faber byte-to-hex surface rather than inventing a consumer-specific
  codec.

## Non-goals

- SHA-384 or SHA-512 before the `modulus<u64>` and literal-carrier prerequisite.
- HMAC, AES-GCM, Ed25519, key generation, Argon2id, or another D3 primitive
  family.
- A `crypta:digere` host route, `host-providers-rs/crates/crypta`, RustCrypto
  dependency, codegen template, `ad` wrapper, shell/process adapter, Python
  adapter, or application-local SHA copy.
- Case-insensitive names, aliases, MD5, Blake2b, or compatibility fallbacks.
- A broad constant-time or secret-memory claim for all cryptography. SHA-256
  uses fixed-round deterministic control flow, but this goal does not silently
  satisfy the prerequisites for secret-bearing D3 families.
- Landing the complete lowercase-hex goal as an unplanned side track.

## Prerequisite gate: `modulus<u32>`

Factory must not start until the modular-word corrective closeout proves all of
the following on the current implementation head:

- conversions between same-width `modulus<u32>` and `numerus<u32>` preserve the
  value and emit compilable Rust;
- scalar and non-path mutable lvalues, including indexed list elements, perform
  modular increment/decrement with a valid assignment place and single
  evaluation;
- every admitted modular operator-method twin emits valid Rust and preserves
  Faber expression grouping when chained, including complement and bitwise
  results used as later receivers;
- the MIR stepper and generated Rust agree on arithmetic, shifts, bitwise
  operations, list storage/retrieval, and the SHA-round fixture;
- Rust debug and release results agree;
- Go, TypeScript, LLVM, Wasm, S-expression, and other unsupported targets reject
  before execution rather than erasing the word family; and
- the generated EBNF Matrix reports the same lowerability measured by the live
  emit paths.

If any item fails, fix Radix and its red-green coverage. Do not work around the
language defect inside Norma.

## Ground truth researched

- `norma/src/crypta.fab`: `digere` and the other eight public cryptographic
  functions remain `mori` deferrals.
- `radix/docs/factory/modular-word-type/goal.md`: native SHA-256 in
  `norma:crypta` is the first consumer and the type exists specifically to make
  the textbook compression algebra structural.
- `radix/docs/factory/modular-word-type/delivery-32-bit.md`: the 32-bit slice
  includes the corrective generated-code and SHA-round closeout receipts.
- `examples/corpus/operatores/modular-word-sha-round.fab`: a live public fixture
  expresses one SHA-256 round with `modulus<u32>`.
- `radix/docs/factory/host-gateway-coverage/goal-d3-crypta.md`: the broader
  profile now makes Faber the canonical deterministic algorithm layer and keeps
  host gateways for irreducible capabilities.
- `norma/src/solum.fab`: file reads already materialize raw `octeti`, so callers
  can supply artifact bytes without a digest-specific I/O adapter.
- `radix/docs/factory/hex-primitive/goal.md`: lowercase hex remains separate and
  is not a digest implementation dependency.
- The Swarm Bootcamp repository and its Python adapter were not found in the
  bounded workspace search. Their replacement remains a handoff claim rather
  than locally verified repository work.

## Reference packet

Before implementation, inspect:

- `../../../../radix/docs/factory/modular-word-type/{goal.md,delivery-32-bit.md}`;
- `../../../../radix/docs/design/numerus-intrinsics.md` and the live modular-word
  semantic/MIR/Rust code paths selected by the prerequisite review;
- `../../../../examples/corpus/operatores/{modular-word.fab,modular-word-sha-round.fab}`;
- `../../../../norma/src/{crypta.fab,solum.fab}` and `../../../../norma/AGENTS.md`;
- `../../../../norma/exempla/README.md` and nearby package-shaped exempla;
- `../../../../faber/src/commands/run.rs` and current package build/run wiring;
- FIPS 180-4 or its current NIST successor for the SHA-256 algorithm and
  authoritative known-answer vectors.

## Constraints and invariants

- The algorithm source of truth is Faber under `norma/src`; generated Rust is a
  validation artifact, never an editable implementation layer.
- All eight initial state words, 64 round constants, schedule words, and working
  variables retain `modulus<u32>` identity through typecheck, HIR, MIR, list
  storage, and supported target emission.
- Padding uses byte length exactly, appends `0x80`, pads with zero bytes, and
  writes the 64-bit big-endian bit length.
- Byte packing and digest serialization are explicitly big-endian.
- Compression performs exactly 64 rounds per 512-bit block and adds the working
  state back into the accumulated state modulo `2^32`.
- No target-language conditional, native dependency, environment probe, or host
  route changes the digest result.
- No plaintext/input bytes appear in diagnostics. Size and algorithm names may
  appear in validation failures.
- A failure on one supported target is a language/compiler defect to repair, not
  permission to fork the Norma algorithm by target.

## Implementation shape

- **Phase 0 — prerequisite acceptance:** rerun the adversarial modular-word
  closeout, land any Radix fixes, regenerate the EBNF Matrix, and record the
  accepted compiler commit.
- **Phase 1 — red vectors and public contract:** change `digere` to its failable
  native contract; add red tests for empty input, `abc`, embedded NUL, malformed
  algorithm names, over-limit input, and lengths 55, 56, 63, 64, and 65 bytes.
- **Phase 2 — native core:** implement padding, schedule, rotations, compression,
  and state accumulation in Faber; prove single-block and multi-block vectors.
- **Phase 3 — public integration:** connect `digere("sha256", data)` to the
  native core, add digest serialization, and keep all other algorithms
  explicitly unsupported.
- **Phase 4 — package and target closeout:** add the Norma package exemplum; run
  Faber/Rust debug/release and MIR proofs; verify unsupported-target rejection;
  inspect generated Rust; rerun matrix and source-library checks.
- **Later goals:** add `modulus<u64>` plus exact literals, then native SHA-384 and
  SHA-512; build HMAC and other deterministic Crypta families in Faber after
  their own language/security prerequisites.

## Acceptance criteria

- `norma:crypta.digere` has a native Faber body and no `mori`, `ad`, codegen
  template, external process, or provider dependency for `sha256`.
- Empty input, `abc`, embedded NUL, padding-boundary, and multi-block vectors
  match authoritative SHA-256 digests byte-for-byte.
- The function returns exactly 32 raw bytes and rejects unsupported algorithm
  names and inputs above 64 MiB through its failable contract.
- The implementation uses `modulus<u32>` throughout compression and contains no
  widening/masking emulation of wrapping arithmetic.
- The same Faber source agrees under Faber round-trip, MIR/reference execution
  where admitted, and generated Rust debug/release execution.
- Unsupported targets fail closed before execution, and the generated EBNF
  Matrix agrees with live lowerability.
- A normal package under `norma/exempla/crypta-sha256/` runs through `faber run`
  and proves the vectors without a test-only host substitute.
- No RustCrypto/Go/TypeScript SHA implementation or `crypta:digere` host route is
  added anywhere in the workspace.

## Validation

From `radix`, run the prerequisite gates selected by the corrective review plus:

```bash
timeout 300 cargo test -p radix modular_word
timeout 300 cargo test -p exempla --lib modular_word
./scripta/check-reader-pack-completeness
./scripta/check-ebnf-vocabulary
./scripta/generate-ebnf-matrix.py
```

From `norma`:

```bash
./scripta/check-source
FABER_LIBRARY_HOME=.. timeout 300 cargo run --manifest-path ../faber/Cargo.toml -- run \
  exempla/crypta-sha256
```

Run the generated Rust proof in both debug and release configurations and
inspect it for explicit wrapping operations, preserved expression grouping, and
the absence of provider/native SHA dependencies. Record exact commands and
commits in the delivery ledger.

## Follow-up items

- Lower and implement `modulus<u64>` plus exact full-range unsigned literals,
  then create native Faber SHA-384/SHA-512 delivery goals.
- Complete the canonical native `octeti` to lowercase-hex surface and update the
  external Bootcamp handoff to prove the exact 64-character rendering path.
- Lower the D3 language/security prerequisites for `clavis`, constant-time
  operations/control flow, secret-memory/zeroization, and bounded execution.

## Open questions

- Which authoritative SHA-256 vector source and exact vector set will the
  delivery ledger pin? The algorithm is fixed, but the evidence source must be
  recorded before implementation.
- Should the private implementation remain in `crypta.fab` for the first slice
  or use `src/crypta/sha256.fab`? Delivery must follow the live Norma module
  convention and keep only `digere` public.
- External verification of the Swarm Bootcamp replacement requires that
  repository's owner and path; it does not block the native Norma implementation.

## Stop conditions

- Stop if the `modulus<u32>` corrective review is not accepted; fix Radix rather
  than encoding a workaround in Norma.
- Stop if implementation requires a Rust/Go/TypeScript SHA dependency, host
  route, shell/process adapter, codegen template, or application-local copy.
- Stop and file a language/compiler prerequisite if padding, schedule mutation,
  byte conversion, modular expression grouping, or target rejection cannot be
  expressed correctly in current Faber.
- Stop before claiming SHA-384/SHA-512 support until `modulus<u64>` and exact
  literal support are complete.
- Stop before release if known-answer, padding-boundary, multi-block,
  debug/release, target-honesty, and package-path proofs are incomplete.
