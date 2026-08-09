# Norma

Norma is the default public Faber source library. Radix resolves imports such as
`norma:json`, `norma:solum/path`, and `norma:chorda` from:

```text
$FABER_LIBRARY_HOME/norma/src/**/*.fab
```

In local Faber development, `FABER_LIBRARY_HOME` is usually the parent
`faberlang/` directory that contains sibling checkouts:

```text
faberlang/
  radix/
  norma/      # this repo
  faber/
  faber-runtime/
  examples/
  cista/
```

This repo owns backend-agnostic Faber source. `src/` is the package interface
root for product `*.fab` modules; colocated `*.proba` test sources are allowed
as test-only (discovered by `faber test`, never importable, not published by
cista install). `src/**/*.fab` must not use `@ externa` or `@ subsidia`; native
Faber bodies, codegen templates, `ad` calls, and explicit `mori` deferrals are
the supported design.

There is no residual Rust `crates/norma` in the private Radix workspace. Generated
Rust packages depend on sibling **`faber-runtime`** (`use faber::…`), not on a
Norma Rust crate.

## Layout

```text
cista.toml     package identity + version (cista install)
src/           public `norma:*` .fab modules and package interface root
exempla/       non-interface examples/tests
scripta/       source-library checks
docs/factory/  open Norma-owned factory goals and planning notes
```

Version in `cista.toml` is **independent of the faber crate**. Product-wise Norma
is a platform default (not listed in app `faber.toml` `[dependencies]`).

Install into the shared package store (from sibling `cista`):

```bash
cargo run -p cista -- install \
  --path ../norma \
  --target-language rust \
  --store "${CISTAE_HOME:-$HOME/.faber/cistae}"
```

That snapshots product `*.fab` under `src/` to
`$CISTAE_HOME/norma/<version>/interfaces/` (interfaces-only; no `libnorma.rlib`;
`*.proba` test sources are excluded from the snapshot). `./scripta/check-source`
allows `*.fab` and test-only `*.proba` under `src/`, and fails on other non-
interface files; docs and instructional exempla stay outside the package
interface root. Dev still uses `FABER_LIBRARY_HOME` until tooling consumes
packaged paths.

## Checks

```bash
./scripta/check-source
faber script scripta/check-promoted-helper-imports.fab
```
