# Test Boundary Hygiene for Norma Stdlib

**Status**: planned — blocked on faber `proba-companion-tests`; discovered 2026-08-10 via housekeeping run on norma
**Created**: 2026-08-10
**Target repo**: `/Users/ianzepp/work/faberlang/norma`
**Factory artifact dir**: `docs/factory/test-boundary-hygiene/`

## Summary

Bring norma into test-boundary compliance: no test code lives inside production
`.fab` files, enforced by a structural ratchet — matching what housekeeping
Step 2/3 already enforces for Rust/TS/Go/Python repos.

## Problem

- `src/model.fab:54` contains an inline `probandum "model metadata facade" { … adfirma … }`
  test block inside a production file — the Faber analog of an inline
  `mod tests` in Rust.
- Norma convention is production `.fab` in `src/` with tests colocated as
  `src/**/*.proba` (discovered via `include_proba`); the inline block breaks that boundary.
- No structural ratchet prevents inline `probandum` / `adfirma` from appearing in
  production files again.
- Extraction is currently blocked: companion `.proba` files cannot resolve local
  module imports (`FaberScript unsupported: provider sym#12/13`) — see faber
  goal `proba-companion-tests`.

## Goals

- Extract `model.fab`'s inline `probandum` block to a colocated companion `.proba`
  (lossless: proba case count unchanged, all cases green) — after the faber
  blocker lands.
- Add a structural ratchet: production `.fab` files must contain no inline
  `probandum` / `proba` / `adfirma` test constructs (budget 0), scoped to
  non-test files.
- Enable housekeeping Step 2/3 on Faber repos (the housekeeping workflow's
  faber branch becomes a real enforcement path, not an unknown).

## Non-goals

- Writing new stdlib tests beyond the extraction (test *quality* is `$polish`'s job).
- Fixing the `json` module compile-gap (SEM016, tracked as `P2 soft_gate`).
- Changing the proba runner semantics (faber goal `proba-companion-tests`).

## Ground Truth Researched

- Housekeeping run `wf_019feb5c7dbc7760b1e6b360f7ca1bb9`, child transcript
  `~/.grok/sessions/…/subagents/019feb5f-7e65-7180-97b2-9f2d0c94b1f1/chat_history.jsonl`:
  the boundary scan found `src/model.fab:54`; single-file `faber test` passes the
  inline cases; extraction path verified non-lossless today.
- `src/mathesis.proba`: the colocated-convention exemplar (`importa ex "./mathesis" privata mathesis`).
- `norma/AGENTS.md`: production/test split for the repo.

## Reference Packet

Before editing, inspect:

- `src/model.fab`: the inline `probandum` block (lines ~54+).
- `src/mathesis.proba`: the companion convention to mirror.
- `radix/scripta/proba-canary.list` + `norma/scripta/check-source`: existing
  enforcement surfaces the ratchet can join.
- Housekeeping skill Step 2 (test-boundary): the doctrine this goal implements.

## Constraints And Invariants

- Extraction must be byte-lossless for test bodies — never edit test cases.
- Proba case count before == after; no case may be dropped or skipped.
- Ratchet scopes to production files; dedicated `*.proba` files are exempt.

## Supporting Skills

- `housekeeping` Step 2/3: the doctrine and the enforcement consumer.
- `faber`: grounding on `faber test` / proba discovery behavior.
- `slice`: the Rust extraction recipes are the model for a future Faber path
  (no Faber recipe exists yet).

## Implementation Shape

- Phase 1 (blocked on faber goal): extract `model.fab` inline `probandum` to
  `src/model.proba`; verify count + green.
- Phase 2: structural ratchet — scan production `.fab` for `probandum`/`adfirma`,
  budget 0, wired into `norma/scripta/check-source` or the housekeeping workflow.
- Phase 3: housekeeping Step 2/3 faber branch — lossless proof uses the proba count.

## Release Posture

Decision: not applicable — source repo, no standalone release.

## Exit Strategy

Decision: not included.

## Acceptance Criteria

- No `probandum` / `adfirma` in production `.fab` files.
- `model.fab` extraction landed with unchanged proba count and all cases green.
- Ratchet exists and fails the build on an injected inline `probandum`.
- Housekeeping Step 2/3 run on norma reports `done` (not `unknown`/`skipped`).

## Validation

- `faber test` proba count before/after extraction — identical.
- Ratchet probe: inject a fake inline `probandum` → check fails; remove → passes.
- Housekeeping run on norma: TestBoundary + Ratchet stages `done`.

## Open Questions

- Does the ratchet live in `norma/scripta/check-source` (repo-native) or in the
  housekeeping workflow's faber branch (shared across Faber repos)?

## Stop Conditions

- Stop if extraction requires editing test bodies or dropping cases.
- Stop if the faber `proba-companion-tests` goal is not landing (blocked state is
  expected and reported, not worked around).
