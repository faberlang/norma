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
  norma/
```

This repo owns backend-agnostic Faber source. `src/**/*.fab` must not use
`@ externa` or `@ subsidia`; native Faber bodies, codegen templates, `ad` calls,
and explicit `mori` deferrals are the supported design.

`crates/norma` in Radix is historical/runtime migration support, not the public
Norma source library.

## Layout

```text
src/      public `norma:*` Faber modules
scripta/  source-library checks
docs/     Norma-owned docs
```

## Checks

```bash
./scripta/check-source
```
