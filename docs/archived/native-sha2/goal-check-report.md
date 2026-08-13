# Goal Check: Native Faber SHA-2 Digests

**Date**: 2026-07-18
**Evaluator mode**: independent cold review
**Artifact**: [`goal.md`](goal.md)
**Intended consumer**: delivery lowering, then factory
**Verdict**: **READY**

## Reasoning

The goal fixes native Faber ownership, the public API and exact failures, the
module-privacy boundary, SHA-2 algorithm scope, Radix prerequisite, NIST vector
authority, implementation path, security non-claims, acceptance, release
posture, and stop conditions. No product or architecture decision remains for
the implementation agent to invent.

## Readiness Evidence

- SHA-256, SHA-384, and SHA-512 names and raw output lengths are exact.
- `digere` has a clean failable contract with exact invalid-algorithm and
  oversize-input text.
- 67,108,864 bytes are admitted; 67,108,865 bytes are rejected before padding.
- True private engines and helpers remain `@ privata` declarations inside
  `src/crypta.fab`; no directly importable helper module is created.
- SHA-384 shares the SHA-512-family core with a distinct IV and 48-byte output.
- The complete modular-width goal is an explicit execution gate.
- NIST FIPS 180-4 and CAVP are authoritative; delivery pins exact vector files.
- Foreign algorithms, providers, generated-source edits, and security claim
  expansion are prohibited.

## Blocking Gaps

None.

## Recommended Next Step

Lower [`goal.md`](goal.md) into a durable delivery specification. Factory
execution remains gated on acceptance of the Radix modular-width delivery.
