# Goal: Retire HAL As A Faber Concept

**Status**: proposed
**Created**: 2026-07-01
**Target repo**: `/Users/ianzepp/work/faberlang/norma`
**Factory artifact dir**: `docs/factory/hal-retirement/`
**Primary surface**: this repo's README/AGENTS and `src/**/*.fab` import guidance;
sibling radix host docs and factory goals that still describe `HAL` as a layer.

---

## Summary

Retire **HAL** as a Faber architecture concept. Faber no longer has a hardware
abstraction layer as a distinct authoring surface or directory model; it has a
standard library built in Faber, and effectful operations leave the language
through syscall-style frame routing (`ad` / `sermo`) to a backend host or kernel
depending on lane.

The target language is:

- `norma:*` is the application standard library (this repo: `src/**/*.fab`).
- `faber:*` is the script/kernel import namespace (private radix / CLI host).
- `ad` / `sermo` is the effect boundary.
- host/kernel routing owns syscall handling.
- There is **no** residual Rust `norma` crate and **no** public HAL tier.
  Runtime carriers live in sibling `faber-runtime` (`use faber::…`).

## Problem

Docs and some host trees still use `HAL` to mean several different things: old
`norma:hal/*` imports, standard-library effect wrappers, host proof
capabilities, and syscall routes. That term now obscures the actual architecture.

The old phrase "hardware abstraction layer" is wrong for current Faber. These
operations are not hardware abstractions; they are standard-library APIs over
host/kernel effects. Keeping `HAL` in docs and paths encourages new code to
recreate a separate layer that the frame gateway design has superseded.

## Goals

- Remove `HAL` as a current architecture term from active README, AGENTS, design
  docs, and open factory goals (this repo first; coordinate sibling radix/faber
  docs as needed).
- Rename active documentation language to "standard library", "runtime backing",
  "host capability", "kernel route", or "frame-routed effect" as appropriate.
- Audit active import guidance so author-facing paths are flat:
  `norma:solum`, `norma:processus`, `faber:solum`, not `norma:hal/solum` or
  `faber:hal/solum`.
- Rename or isolate code module paths where `hal` is only historical packaging
  (e.g. sibling radix `hosts/macos-arm64/src/hal`), if the rename can be done
  without confusing public APIs.
- Preserve release-history references as history, but mark them as historical
  where needed.
- Update tests and docs so they assert the clean-break policy: no new active
  `HAL` surfaces, aliases, or compatibility imports.

## Non-goals

- Redesigning `ad`, `sermo`, frame payloads, route manifests, or gateway
  forwarding.
- Migrating the entire standard library body model in this goal.
- Rewriting release notes or archived historical material except to avoid
  misleading current docs.
- Preserving old `norma:hal/*` or `faber:hal/*` imports.

## Ground Truth Researched

- This repo's `README.md` / `AGENTS.md` already forbid public `norma:hal/*`
  imports and require flat modules such as `norma:solum`.
- Sibling radix `docs/factory/frame-gateway/` (closed) defines the current effect
  model: `ad` opens a frame-stream conversation; route strings are opaque.
- Sibling radix kernel/script goals require flat `faber:*` paths and reject
  `faber:hal/*`.
- Sibling `../faber` package tests contain rejection coverage for
  `norma:hal/solum`.
- Residual Rust `crates/norma` (including any `hal/` subtree) is **removed** from
  the private workspace; do not plan work against it.
- Host proof trees under sibling radix may still name `hal` directories
  historically.

## Reference Packet

- `README.md`, `AGENTS.md` (this repo)
- `src/**/*.fab` (public Norma source)
- sibling radix `docs/design/frame-stream-types.md`
- sibling radix `docs/factory/frame-gateway/` (historical campaign)
- sibling radix `docs/factory/faber-script-kernel/` (closed; pointer/stub)
- sibling radix `hosts/macos-arm64/` (host capability naming)
- sibling `../faber/src/package_test.rs` (legacy import rejection)

## Constraints And Invariants

- There is no author-facing HAL namespace after this goal.
- `HAL` must not be replaced by a vague synonym that keeps the same obsolete
  layer alive.
- Frame-routed effects are syscall-style conversations through `ad` / `sermo`;
  the compiler must not infer behavior from route names.
- Script/kernel imports stay flat under `faber:*`.
- Application stdlib imports stay flat under `norma:*` from this repo's `src/`.
- Historical release notes may keep the old term when clearly historical.

## Implementation Shape

1. Documentation pass: update this repo's README/AGENTS and any Norma-owned docs;
   file sibling radix/faber doc PRs for remaining active `HAL` language.
2. Import policy pass: ensure active docs and examples do not recommend
   `norma:hal/*` or `faber:hal/*` as current surfaces.
3. Host naming pass (sibling radix): evaluate renaming
   `hosts/macos-arm64/src/hal` only if tests prove a clean break.
4. Test pass: keep rejection tests for `norma:hal/*` and `faber:hal/*` in sibling
   `faber`; add hygiene that flags new active-doc `HAL` usage outside approved
   historical paths.
5. Closeout pass: record the vocabulary in this repo's README.

## Acceptance Criteria

- Active docs describe Faber effects as standard-library APIs over frame-routed
  host/kernel syscalls, not as HAL.
- No active authoring guide recommends `norma:hal/*` or `faber:hal/*`.
- Existing rejection tests for legacy HAL imports still pass.
- Any remaining `HAL` mentions are historical, release-note-only, or explicitly
  marked transitional debt.
- No compatibility aliases are added for old HAL import paths.

## Validation

From this repo and siblings:

```bash
# Norma source policy
./scripta/check-source

# No public HAL imports in Norma source
! rg -n 'norma:hal|faber:hal' src

# Sibling faber still rejects legacy HAL imports
cargo test --manifest-path ../faber/Cargo.toml -- package

# Optional broader audit (expect historical hits until filtered)
rg -n '\bHAL\b|norma:hal|faber:hal' README.md AGENTS.md docs src \
  ../radix/docs ../radix/hosts ../faber/src
```

The broad `rg` is an audit gate, not an expected zero-result command until
approved historical paths are filtered or documented.

## Open Questions

- Preferred replacement names for residual host `hal` directories in sibling
  radix: `backing`, `host`, `effect`, `syscall`, or `capability`?
- Should release notes and legacy website snapshots be excluded from automated
  hygiene checks by path, or annotated in place?

## Stop Conditions

- Stop if cleanup starts reintroducing compatibility aliases for old HAL paths.
- Stop if a rename would break a real external crate API without an intentional
  clean-break decision.
- Stop if the work starts redesigning frame routing or `sermo` materialization
  instead of retiring obsolete HAL vocabulary.
