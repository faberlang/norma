# Norma Agent Instructions

Norma is the public backend-agnostic Faber source library for `norma:*`
imports. This repo owns `.fab` source under `src/`; Radix consumes it through
`FABER_LIBRARY_HOME`, usually the parent `faberlang/` directory in local
development.

## Rules

- Keep public modules under `src/**/*.fab`.
- Do not add `@ externa` or `@ subsidia`; Norma source should stay native Faber,
  codegen-template, `ad`, or explicit `mori` deferral based.
- Do not reintroduce public `norma:hal/*` imports. Use flat modules such as
  `norma:solum`, `norma:json`, and nested modules such as `norma:solum/path`.
- `crates/norma` in Radix is not this repo and must not be treated as the
  canonical source library.

## Validation

Run `./scripta/check-source` from this repo after source changes.
