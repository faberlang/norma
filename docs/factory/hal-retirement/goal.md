# Goal: Retire HAL As A Faber Concept

**Status**: proposed
**Created**: 2026-07-01
**Target repo**: `/Users/ianzepp/work/faberlang/norma`
**Factory artifact dir**: `docs/factory/hal-retirement/`
**Primary surface**: stdlib docs/imports, `crates/norma` runtime backing names,
host capability docs, factory goals that still describe `HAL` as a layer.

---

## Summary

Retire **HAL** as a Faber architecture concept. Faber no longer has a hardware
abstraction layer as a distinct authoring surface or directory model; it has a
standard library built in Faber, and effectful operations leave the language
through syscall-style frame routing (`ad` / `sermo`) to a backend host or kernel
depending on lane.

The target language is:

- `norma:*` is the application standard library.
- `faber:*` is the script/kernel import namespace.
- `ad` / `sermo` is the effect boundary.
- host/kernel routing owns syscall handling.
- Rust files under `crates/norma` are runtime backing for `@ externa`, not a
  public HAL tier.

## Problem

The repo still uses `HAL` to mean several different things: old `norma:hal/*`
imports, standard-library effect wrappers, Rust backing modules, host proof
capabilities, and syscall routes. That term now obscures the actual architecture.

The old phrase "hardware abstraction layer" is wrong for current Faber. These
operations are not hardware abstractions; they are standard-library APIs over
host/kernel effects. Keeping `HAL` in docs and paths encourages new code to
recreate a separate layer that the frame gateway design has superseded.

## Goals

- Remove `HAL` as a current architecture term from README, AGENTS, design docs,
  and active factory goals.
- Rename active documentation language to "standard library", "runtime backing",
  "host capability", "kernel route", or "frame-routed effect" as appropriate.
- Audit active import guidance so author-facing paths are flat:
  `norma:solum`, `norma:processus`, `faber:solum`, not `norma:hal/solum` or
  `faber:hal/solum`.
- Rename or isolate code module paths where `hal` is only historical packaging,
  especially `crates/norma/hal` and `hosts/macos-arm64/src/hal`, if the rename
  can be done without confusing the public crate API.
- Preserve release-history references and legacy website snapshots as history,
  but mark them as historical where needed.
- Update tests and docs so they assert the clean-break policy: no new active
  `HAL` surfaces, aliases, or compatibility imports.

## Non-goals

- Redesigning `ad`, `sermo`, frame payloads, route manifests, or gateway
  forwarding.
- Migrating the entire standard library to `ad` in this goal.
- Removing `@ externa`; runtime backing remains valid for irreducible host
  calls and performance kernels.
- Rewriting release notes or archived historical material except to avoid
  misleading current docs.
- Preserving old `norma:hal/*` or `faber:hal/*` imports.

## Ground Truth Researched

- `stdlib/norma/README.md` already says flat `norma:*` has retired
  `norma:hal/*`, but still names a `HAL` layer and `HAL` floor.
- `docs/factory/frame-gateway/goal.md` defines the current effect model:
  `ad` opens a frame-stream conversation; route strings are opaque; typed APIs
  live above the primitive.
- `docs/factory/faber-script-kernel/goal.md` and related kernel goals already
  require flat `faber:*` paths and reject `faber:hal/*`.
- `crates/faber-cli/src/package_test.rs` contains rejection coverage for
  `norma:hal/solum`.
- Active code still has `crates/norma/hal/*` and `hosts/macos-arm64/src/hal/*`
  module names.
- `README.md`, `AGENTS.md`, host docs, and many factory/design docs still use
  `HAL` as current architectural language.

## Reference Packet

- `README.md`
- `AGENTS.md`
- `stdlib/norma/README.md`
- `docs/factory/frame-gateway/goal.md`
- `docs/design/frame-stream-types.md`
- `docs/factory/faber-script-kernel/goal.md`
- `docs/factory/faber-kernel-solum/goal.md`
- `docs/factory/faber-kernel-processus/goal.md`
- `crates/norma/lib.rs`
- `crates/norma/hal/`
- `hosts/macos-arm64/src/hal/`
- `crates/faber-cli/src/package_test.rs`

## Constraints And Invariants

- There is no author-facing HAL namespace after this goal.
- `HAL` must not be replaced by a vague synonym that keeps the same obsolete
  layer alive.
- `@ externa` means runtime backing for a standard-library declaration; it does
  not imply a HAL module.
- Frame-routed effects are syscall-style conversations through `ad` / `sermo`;
  the compiler must not infer behavior from route names.
- Script/kernel imports stay flat under `faber:*`.
- Application stdlib imports stay flat under `norma:*`.
- Historical release notes may keep the old term when clearly historical.

## Implementation Shape

1. Documentation pass: update active README, AGENTS, stdlib README, frame gateway
   docs, host docs, and current factory goals to use the new vocabulary.
2. Import policy pass: ensure active docs and examples do not recommend
   `norma:hal/*`, `faber:hal/*`, or `stdlib/norma/hal/*` as current surfaces.
3. Code naming pass: evaluate renaming `crates/norma/hal` to a runtime-backing
   name such as `host` or `backing`, and `hosts/macos-arm64/src/hal` to
   `capability` or `syscall`. Rename only if tests can prove the change without
   preserving obsolete public aliases.
4. Test pass: keep or add rejection tests for `norma:hal/*` and `faber:hal/*`;
   add a hygiene check that flags new active-doc `HAL` usage outside approved
   historical paths.
5. Closeout pass: update release notes for this cleanup and record the new
   vocabulary in the stdlib and frame-gateway docs.

## Acceptance Criteria

- Active docs describe Faber effects as standard-library APIs over frame-routed
  host/kernel syscalls, not as HAL.
- No active authoring guide recommends `norma:hal/*`, `faber:hal/*`, or
  `stdlib/norma/hal/*`.
- Runtime backing remains available for `@ externa` declarations without being
  presented as a public HAL tier.
- Existing rejection tests for legacy HAL imports still pass.
- Any remaining `HAL` mentions are historical, release-note-only, or explicitly
  marked transitional debt.
- No compatibility aliases are added for old HAL import paths.

## Validation

```bash
rg -n "\bHAL\b|\bhal\b|norma:hal|faber:hal|stdlib/norma/hal" README.md AGENTS.md docs stdlib hosts crates
cargo test -p faber-cli -- package
cargo test -p radix -- kernel_namespace
cargo test -p norma
```

The `rg` command is an audit gate, not an expected zero-result command until
approved historical paths are filtered or documented.

## Open Questions

- Should `crates/norma/hal` be renamed in the first implementation, or should
  that be a second slice after docs and import policy are clean?
- Preferred replacement names: `backing`, `host`, `effect`, `syscall`, or
  `capability`?
- Should release notes and legacy website snapshots be excluded from automated
  hygiene checks by path, or annotated in place?

## Stop Conditions

- Stop if cleanup starts reintroducing compatibility aliases for old HAL paths.
- Stop if a rename would break a real external crate API without an intentional
  clean-break decision.
- Stop if the work starts redesigning frame routing or `sermo` materialization
  instead of retiring obsolete HAL vocabulary.
