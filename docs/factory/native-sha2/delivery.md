# Delivery: Native Faber SHA-2 Digests

**Status**: ready for factory after campaign Stage 1 acceptance
**Created**: 2026-07-18
**Vision source**: [`goal.md`](goal.md)
**Goal check**: [`goal-check-report.md`](goal-check-report.md) — READY
**Primary repo**: `/Users/ianzepp/work/faberlang/norma`
**Validation repos**: `radix` and `faber`
**Campaign stage**: Native Faber Crypta Stage 2
**Hard dependency**: accepted Radix
[`modular-word-width-family`](../../../../radix/docs/factory/modular-word-width-family/delivery.md)
commit on main
**Checkpoint**: `norma:crypta.digere` ships native SHA-256/384/512 through a
normal Faber package path

## Interpreted Unit

Replace the deferred digest stub with one Faber-authored SHA-2 implementation
surface. Land the public failable API, authoritative red vectors, a private
32-bit SHA-256 engine, a private shared 64-bit SHA-384/SHA-512 engine, raw digest
serialization, package proofs, and independent closeout evidence. Generated
Rust is executed and inspected but never edited or treated as algorithm source.

Explicit requirements:

- exact `digere(textus, octeti) → octeti ⇥ textus` contract;
- exact lowercase algorithm names and error text;
- exact 64 MiB resource boundary;
- complete SHA-256, SHA-384, and SHA-512 in `src/crypta.fab`;
- `@ privata` implementation helpers with no nested public helper modules;
- `modulus<u32>` and `modulus<u64>` compression words;
- NIST FIPS/CAVP known-answer evidence;
- ordinary compiled `faber run`, debug/release, and available MIR/reference
  execution receipts;
- no provider, host route, foreign dependency, or generated-source edit.

## Normalized Spec

### Public API

```fab
functio digere(textus alg, octeti data) → octeti ⇥ textus
```

Accepted names and outputs:

| Name | Engine | Output |
| --- | --- | --- |
| `sha256` | private 32-bit SHA-256 | 32 raw bytes |
| `sha384` | private shared SHA-512-family engine with SHA-384 IV | first 48 raw bytes |
| `sha512` | private shared SHA-512-family engine with SHA-512 IV | 64 raw bytes |

Exact failures:

- invalid name: `norma:crypta.digere: algorithm must be sha256, sha384, or sha512`;
- input above limit: `norma:crypta.digere: input exceeds 67108864-byte limit`.

67,108,864 bytes are admitted. 67,108,865 bytes reject before padding.

### Constraints

- All implementation code remains Faber in `src/crypta.fab`.
- Every engine/helper is `@ privata`; tests enter through public `digere`.
- No `src/crypta/sha*.fab` path is created under the current public interface
  model.
- No source input appears in failure text.
- Raw bytes remain raw; encoding and file I/O stay outside `digere`.
- The Stage 1 accepted Radix commit is recorded before N2 begins.

### Non-goals

Use the goal's non-goals without expansion: HMAC, encryption, signatures, KDFs,
streaming, other hash families, encoding, providers, and broad security claims
remain outside this delivery.

## Repo-Aware Baseline

| Surface | Current truth | Delivery consequence |
| --- | --- | --- |
| `norma/src/crypta.fab` | every function is `mori`; `digere` returns non-failable octeti | clean-break signature and body |
| Norma interface root | every `src/**/*.fab` path is importable | keep true private helpers in facade file |
| `octeti` | unifies with `lista<numerus<u8>>` | use standard list access/mutation for bytes |
| `modulus<u32>` | accepted compiler baseline | SHA-256 may start after prerequisite receipt |
| `modulus<u64>` | campaign Stage 1 output | SHA-384/SHA-512 bodies wait for acceptance |
| package execution | compiled Rust is ordinary `faber run` default | package exemplum is primary product proof |
| MIR execution | capability depends on full module/package graph | use where admitted; never fork algorithm |
| NIST evidence | FIPS 180-4 plus CAVP byte response vectors | pin exact archive and cases before green code |

Primary starting paths:

- `norma/src/crypta.fab`;
- `norma/exempla/` package convention and new `exempla/crypta-sha2/`;
- sibling Faber package discovery/build/run wiring;
- Radix modular operators, byte/list intrinsics, and target gates only when a
  consumer proof exposes a compiler defect.

## Stage Graph

```text
N0 prerequisite receipt + pinned red vectors + public API
                     |
                     v
N1 native SHA-256 engine and public sha256 path
                     |
                     v
N2 shared SHA-512-family engine + SHA-384/SHA-512
                     |
                     v
N3 package, cross-lane, adversarial, and release closeout
```

### N0 — Prerequisite receipt, vectors, and API

Inputs: Stage 1 accepted Radix commit; current deferred facade; NIST sources.

Outputs:

- delivery ledger records exact Radix/Faber commits and modular-width gates;
- exact CAVP byte-vector archive URL, response filenames, checksums, and selected
  empty/`abc`/binary/padding/multi-block cases;
- red package tests for all selected cases and exact API failures;
- clean-break `digere` signature and algorithm dispatch skeleton;
- red 67,108,864 accepted and 67,108,865 rejected resource-boundary proof.

Gate: vector expectations are fixed independently of implementation output;
all algorithm paths are red for the expected missing native bodies.

### N1 — Native SHA-256

Inputs: N0 vectors and accepted `modulus<u32>`.

Outputs:

- private initial state and round constants;
- 512-bit padding and 64-bit big-endian bit length;
- byte-to-word packing, 64-word schedule, rotations, choose/majority, 64 rounds,
  accumulated state, and 32-byte big-endian serialization;
- public `digere("sha256", data)` integration;
- single-block, padding-boundary, binary, and multi-block green vectors.

Gate: SHA-256 passes through compiled Faber debug/release and the normal package
path without widening/masking wrap emulation or foreign dependencies.

### N2 — Shared SHA-512 family

Inputs: accepted N1 pattern and Stage 1 `modulus<u64>`.

Outputs:

- private 64-bit constants, 1024-bit padding, 128-bit length field, byte/word
  conversion, 80-word schedule, rotations, functions, 80-round compression,
  and state accumulation;
- SHA-512 configuration and 64-byte serialization;
- SHA-384 distinct IV and 48-byte truncation over the same engine;
- upper-half `u64` constants written directly as `modulus<u64>` literals;
- green CAVP vectors for both algorithms.

Gate: source inspection proves one shared 64-bit engine; SHA-384 is not a copied
compression implementation; all selected vectors pass.

### N3 — Public package and closeout

Inputs: accepted N1 and N2 engines.

Outputs:

- package-shaped `exempla/crypta-sha2/` with `faber.toml` and `src/main.fab`;
- exact unsupported-name, resource, output length, embedded-zero, padding, and
  multi-block public proofs;
- generated Rust debug/release agreement and review for explicit wrapping;
- MIR/reference receipt where admitted, or an exact capability limitation with
  no alternate implementation;
- source check, import check, Radix regression, correctness, authorized
  adversarial, cleanliness, housekeeping, and polish receipts;
- Norma/Faber release-prep note; no publication.

Gate: `digere` is usable by an ordinary Faber package and no algorithm source
or dependency exists outside Norma `.fab`.

## Implementation Work

| Workstream | Write surface | Done when |
| --- | --- | --- |
| Contract/vectors | `src/crypta.fab`, package exemplum, delivery ledger | exact red public evidence |
| SHA-256 | private declarations in `src/crypta.fab` | all 32-bit vectors green |
| SHA-512 family | private declarations in `src/crypta.fab` | SHA-384/512 share one green core |
| Package proof | `exempla/crypta-sha2/` | ordinary `faber run` succeeds |
| Compiler repair, only if exposed | narrow Radix/Faber defect path | repaired upstream with regression; no Norma workaround |
| Closeout | docs/index/release-prep artifacts | campaign gate evidence complete |

The algorithm work is sequential inside one file. Tests, vector curation, and
read-only review may proceed independently because they do not share source
write surfaces. Any required Radix repair becomes a separate factory phase in
its owning repo before Norma resumes.

## Checkpoints And Gates

### Batching / Split Decision

Decision: split on the real 32-bit versus 64-bit engine boundary, then batch
SHA-384 and SHA-512 over one shared 64-bit implementation. Do not split by
round, constant table, vector, or output word.

### Factory vision record

- Goal boundary: native Faber SHA-256/384/512 under one public digest API.
- Non-goals: every other Crypta family and every foreign implementation.
- Acceptance: pinned NIST bytes through ordinary package execution.
- Stop conditions: missing Radix prerequisite, foreign fallback, public helper
  leakage, duplicated SHA-384 core, self-generated oracle, or incomplete
  release evidence.
- Vision verdict: READY, supported by independent goal-check.

### Production ledger

- Current unit: N0 only until campaign Stage 1 is accepted.
- Generated spec: this file.
- Pending implementation stages: N0–N3.
- Blocked stages: N2 and final campaign acceptance until Radix Stage 1 main
  receipt; execution of the entire delivery waits by campaign rule.
- Repo boundary: Norma owns source; Radix/Faber repairs require separate commits
  and campaign bookkeeping.
- Release decision: `release-prep`; no publication.

## Validation

```bash
cd /Users/ianzepp/work/faberlang/norma
./scripta/check-source
./scripta/check-promoted-helper-imports
FABER_LIBRARY_HOME=.. timeout 300 cargo run --manifest-path ../faber/Cargo.toml -- check src/crypta.fab
FABER_LIBRARY_HOME=.. timeout 300 cargo run --manifest-path ../faber/Cargo.toml -- run exempla/crypta-sha2
```

From Radix, each filter separately:

```bash
timeout 300 cargo test -p radix modular_word
timeout 300 cargo test -p exempla --lib crypta
cargo build --manifest-path ../faber/Cargo.toml
./scripta/test
```

Run generated Rust in debug and release. Compare raw output bytes directly to
the pinned CAVP response values. Inspect dependency manifests and generated
source for absence of foreign cryptographic implementations.

## Companion Skill Plan

- `faber` for all Norma source and package work.
- `red-green` for N0 vector ownership.
- `correctness` after each engine and at N3.
- `black-hat` at N3 for authorized adversarial API and boundary review; do not
  infer secret-bearing guarantees.
- `cleanliness` and `polish` on `src/crypta.fab` after each engine because one
  private implementation file will grow substantially.
- `poker-face` against goal/campaign ownership and acceptance promises.

## Open Questions

None block delivery lowering. N0 must pin exact CAVP archive checksums before
implementation. MIR package execution capability is evidence to record, not a
reason to fork or defer the compiled Faber implementation.
