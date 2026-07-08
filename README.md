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

This repo owns backend-agnostic Faber source. `src/**/*.fab` must not use
`@ externa` or `@ subsidia`; native Faber bodies, codegen templates, `ad` calls,
and explicit `mori` deferrals are the supported design.

There is no residual Rust `crates/norma` in the private Radix workspace. Generated
Rust packages depend on sibling **`faber-runtime`** (`use faber::…`), not on a
Norma Rust crate.

## Layout

```text
src/           public `norma:*` Faber modules
scripta/       source-library checks
docs/factory/  open Norma-owned factory goals (when present)
```

## Checks

```bash
./scripta/check-source
```
