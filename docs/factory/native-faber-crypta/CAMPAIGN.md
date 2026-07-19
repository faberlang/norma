# Campaign: Native Faber Crypta

**Status**: active — Stages 1 and 2 goals READY and lowered; Stage 1 is ready
for factory execution
**Created**: 2026-07-18
**Control plane repo**: `/Users/ianzepp/work/faberlang/norma`
**Participating repos**: `radix`, `norma`, `faber`, and `examples`
**Next stage**: Stage 1 — complete the unsigned modular word family in Radix
**Release posture**: prepare releases at accepted checkpoints; do not publish
without separate operator authorization

## Summary

Coordinate the language prerequisites and standard-library delivery required to
make `norma:crypta` a Faber-authored cryptography library. Deterministic
algorithms belong in Norma `.fab` source. Radix supplies missing language
guarantees. Generated Rust is an execution artifact, and host gateways remain
limited to irreducible operating-system or hardware capabilities such as secure
entropy.

The first campaign checkpoint completes every unsigned modular width and ships
native Faber SHA-256, SHA-384, and SHA-512. Later Crypta families lower only
after their arithmetic, secret-memory, constant-time, key-type, entropy, and
bounded-execution prerequisites have explicit goals.

## Problem

- `norma/src/crypta.fab` advertises multiple cryptographic families but every
  function remains a fatal deferral.
- Prior provider-oriented planning risked treating a language limitation as a
  permanent reason to own deterministic algorithms in Rust.
- The `modulus<u32>` proof now establishes the correct Faber algebra for
  SHA-256, but the type family and scalar carrier remain incomplete for
  SHA-384/SHA-512.
- Existing Radix D3 planning spans digests, HMAC, encryption, signatures, key
  generation, and KDFs. Those tracks have real dependency boundaries and must
  not enter one factory phase as a monolith.

## Desired End State

- Norma owns deterministic cryptographic algorithm source in Faber.
- Radix implements the language semantics required by those algorithms rather
  than accepting target-language substitutes.
- `norma:crypta.digere` ships native SHA-256, SHA-384, and SHA-512 with pinned
  authoritative vectors.
- HMAC, AES-GCM, Ed25519, and Argon2id follow the same ownership rule after
  their security and runtime prerequisites land.
- Host code owns only narrow effects that Faber cannot synthesize, principally
  operating-system entropy and hardware/service boundaries.
- Public documentation, target matrices, package examples, and release claims
  never outrun executable evidence.

## Development Posture

- Clean break: deferred or misleading Crypta contracts are replaced, not
  wrapped with compatibility shims.
- Faber first: a missing capability files a Radix/runtime prerequisite.
- Fail closed: unsupported algorithms and targets reject explicitly.
- No algorithm fallback: generated Rust, RustCrypto, providers, shell commands,
  and application-local copies cannot stand in for Norma source.
- Security claims are capability-specific. Digest correctness does not imply
  constant-time secret processing, zeroization, secure entropy, or FIPS module
  validation.
- Multiple repositories share a workspace. Each factory phase owns one primary
  repo; cross-repo acceptance happens only at named campaign gates.

## Implementation Workflow

```text
campaign stage
    → goal-forge artifact
    → independent goal-check READY
    → delivery spec
    → factory phase(s)
    → correctness/security review
    → campaign acceptance gate
```

The campaign routes work and records gates. It never implements directly.
Radix phases use the `faber`, `factory`, `red-green`, and `correctness` skills.
Norma cryptographic phases additionally require authorized `black-hat` review
before a public release claim.

## Scope Routing

### In campaign

- Exact unsigned scalar and modular-word language prerequisites.
- Native SHA-2 digests.
- Native HMAC-SHA-2 after digest and `clavis` prerequisites.
- Native AES-256-GCM, Ed25519, and Argon2id after separately accepted security
  and runtime prerequisites.
- Narrow OS entropy integration where an algorithm or key-generation contract
  requires it.
- Package examples, known-answer vectors, target honesty, and release evidence.

### Split out

- TLS, certificates, keychains, secret managers, password vaults, and envelope
  formats.
- SHA-1, MD5, Blake2, SHA-3, RSA, AES-CBC, PBKDF2, and scrypt unless a later
  campaign revision explicitly admits them.
- FIPS module certification.
- Broad compiler backend parity unrelated to the active Crypta consumer.

## Batching And Split Policy

- Stage 1 is `discovery-first`, then batch all four unsigned widths once the
  exact unsigned carrier and serialized-MIR shape are proven.
- Stage 2 is `split-on-boundary`: SHA-256 establishes the 32-bit engine; the
  shared SHA-512-family engine then batches SHA-384 and SHA-512. The split is
  justified by word width, block shape, and prerequisite timing.
- Later algorithm families always split on secret-data policy, entropy,
  constant-time arithmetic, memory/zeroization, or bounded-execution boundaries.
- Do not create one factory phase per constant, width, vector, or algorithm
  round. Batch homogeneous tables and vectors after the first pattern is green.

## Ground Truth Researched

- `norma/src/crypta.fab`: nine public cryptographic functions remain `mori`.
- `norma/AGENTS.md`: public source under `src/` must remain native `.fab`; no
  `@ externa` or `@ subsidia` implementation escape exists.
- `radix/docs/factory/host-gateway-coverage/goal-d3-crypta.md`: deterministic
  algorithms are Faber-owned; host gateways are limited to irreducible seams.
- `radix/docs/factory/modular-word-type/delivery-32-bit.md`: `modulus<u32>` is
  ship-clear after focused, full, backend, conversion, and mutation repair.
- `radix/crates/radix/src/semantic/types.rs`: modular words already have a
  width-parametric semantic identity.
- `radix/crates/radix/src/{syntax,hir,mir}` and the MIR stepper: signed `i64`
  carriers block exact upper-half `u64` constants.
- NIST FIPS 180-4 and CAVP publish the SHA-2 specification and byte-oriented
  verification vectors.
- Operator decision, 2026-07-18: expand `modulus` across all unsigned widths and
  use it to make Norma Crypta completely Faber-authored.

## Current State

| Track | State | Next action |
| --- | --- | --- |
| `modulus<u32>` baseline | complete and ship-clear | preserve as regression authority |
| all-width modular family | goal-check READY; delivery saved | run the Radix factory delivery |
| native SHA-2 | goal-check READY; delivery saved; execution blocked on all-width family | execute after Stage 1 acceptance |
| HMAC-SHA-2 | routed, not yet goal-forged | wait for native SHA-2 plus `clavis` decision |
| AES-GCM / Ed25519 / Argon2id | deferred prerequisites | lower separate goals only after security substrate evidence |
| irreducible entropy | unselected | retain as narrow host-capability track |

## Campaign Path

### Stage 1 — Complete unsigned modular words

- **Status**: selected; goal-check READY; delivery saved; ready for factory.
- **Source**: [`radix modular-word-width-family goal`](../../../../radix/docs/factory/modular-word-width-family/goal.md).
- **Delivery**: [`radix modular-word-width-family delivery`](../../../../radix/docs/factory/modular-word-width-family/delivery.md).
- **Why now**: it removes the language blocker for SHA-384/SHA-512 and finishes
  the type family rather than landing another single-width exception.
- **Batching**: discovery-first, then batch all widths.
- **Gate**: all widths and upper-half `u64` values agree in semantics, Rust,
  Faber round-trip, and MIR stepper; unsupported targets fail closed.
- **Overlap rule**: Norma SHA-256 planning may proceed, but no SHA-384/SHA-512
  implementation starts until this gate is accepted on Radix main.
- **Lowers to**: delivery, then factory.

### Stage 2 — Native Faber SHA-2

- **Status**: goal-check READY; delivery saved; execution blocked
  on Stage 1 acceptance.
- **Source**: [`native-sha2 goal`](../native-sha2/goal.md).
- **Delivery**: [`native-sha2 delivery`](../native-sha2/delivery.md).
- **Why now**: it is the first complete standard-library consumer and proves
  that the compiler work ships user-visible Faber capability.
- **Batching**: split on the 32-bit versus shared 64-bit engine boundary.
- **Gate**: native `digere` passes pinned NIST vectors for SHA-256/384/512 via a
  normal Faber package, with no foreign implementation or provider path.
- **Overlap rule**: red vector and API-contract work may begin after Stage 1's
  architecture checkpoint, but 64-bit algorithm bodies wait for Stage 1 close.
- **Lowers to**: delivery, then Norma factory.

### Stage 3 — Native HMAC-SHA-2

- **Status**: deferred; no goal created yet.
- **Source**: Radix D3 ownership profile and future `clavis` goal.
- **Gate**: native SHA-2 accepted; secret-key type and handling contract ready.
- **Batching**: batch SHA-256/384/512 variants over one HMAC construction.
- **Lowers to**: goal-forge, goal-check, delivery, then factory.

### Stage 4 — Secret-bearing algorithm families

- **Status**: deferred; not selected.
- **Source**: Radix D3 ownership profile.
- **Gate**: accepted goals for constant-time arithmetic/control flow,
  secret-memory and zeroization, bounded execution, exact key formats, and
  reviewed OS entropy.
- **Batching**: split by AES-GCM, Ed25519, and Argon2id security boundaries.
- **Lowers to**: separate goals and deliveries; never one monolithic phase.

### Stage 5 — Crypta release closeout

- **Status**: deferred.
- **Gate**: all selected public algorithms have independent correctness and
  adversarial receipts, package examples, stable failure contracts, and honest
  capability claims.
- **Lowers to**: release preparation; publication requires operator approval.

## Dependency Rules

1. Norma never works around a missing Radix guarantee.
2. Stage 2 execution requires Stage 1 acceptance on Radix main, not merely a
   packet branch or passing focused test.
3. HMAC requires both native digest acceptance and an explicit secret-key
   contract; raw `octeti` keys do not silently become the final public policy.
4. Algorithms that need entropy consume a narrow reviewed OS entropy seam.
   Entropy access does not move deterministic algorithm ownership to the host.
5. Secret-bearing algorithms cannot inherit digest-only security claims.
6. Generated output is inspected and executed but never edited as source.
7. Each repository commits its own coherent factory phase; campaign acceptance
   records exact commits before advancing dependencies.

## First Useful Milestones

- M1: `modulus<u8|u16|u32|u64>` and exact unsigned `u64` values are ship-clear.
- M2: native Faber SHA-256 replaces the first `digere` deferral.
- M3: native Faber SHA-384 and SHA-512 share one accepted 64-bit core.
- M4: `norma:crypta.digere` ships the complete selected SHA-2 surface through
  a normal package path.

## Acceptance Criteria

- Every active campaign stage points to one authoritative goal and delivery
  spec before factory execution.
- The next stage is unambiguous and dependency gates prevent premature Norma
  workarounds.
- Algorithm ownership remains Faber/Norma in every child artifact.
- Later secret-bearing tracks remain routed but unimplemented until their
  security prerequisites are explicit.
- Release preparation occurs at M1 and M4; no publication is implied.
- Campaign completion is not claimed at M4. M4 completes the SHA-2 checkpoint;
  later selected Crypta families continue under this control plane.

## Validation

Planning validation:

```bash
cd /Users/ianzepp/work/faberlang/radix
./scripta/check-factory-goal-status --fail-on error

cd /Users/ianzepp/work/faberlang/norma
./scripta/check-source
```

Implementation validation belongs to the child delivery specs. Campaign
acceptance additionally records exact accepted commits from each repository and
the normal `faber run` package receipt for public Norma algorithms.

## Open Questions

None block Stages 1 or 2 lowering. Stage 3 requires a separate accepted
`clavis`/secret-key decision before goal-forge begins.

## Stop Conditions

- Stop if any deterministic algorithm is routed to a foreign implementation
  because Faber lacks a feature; file the language prerequisite instead.
- Stop if a campaign stage lacks an independently READY goal and durable
  delivery spec.
- Stop before secret-bearing algorithm work without explicit constant-time,
  secret-memory, key-format, and entropy boundaries.
- Stop before external publication, package release, or deployment without
  fresh operator authorization.
- Stop if repository dirt would require overwriting another session's work.
