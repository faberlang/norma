# Norma Agent Instructions

Norma is the public backend-agnostic Faber source library for `norma:*`
imports. This repo owns `.fab` source under `src/`; Radix consumes it through
`FABER_LIBRARY_HOME`, usually the parent `faberlang/` directory in local
development.

## Rules

- Keep public modules under `src/**/*.fab`.
- Keep stdlib instructional demos under `exempla/**/*.fab` (not in the language
  keyword corpus under sibling `examples/corpus/`).
- Do not add `@ externa` or `@ subsidia`; Norma source should stay native Faber,
  codegen-template, `ad`, or explicit `mori` deferral based.
- Do not reintroduce public `norma:hal/*` imports. Use flat modules such as
  `norma:solum`, `norma:json`, and nested modules such as `norma:solum/path`.
- Do not treat any historical Radix `crates/norma` path as the source library —
  that crate is removed; this repo is canonical.
- **Verb conjugation / method posture:** follow sibling radix
  [`docs/stdlib/morphologia.md`](../radix/docs/stdlib/morphologia.md)
  (authoritative). Hand-expand conjugations consistently with long-term
  stem+elaborator vision: one stem per operation, real Latin forms, Axis A
  (time/flow) and Axis B (ownership) as documented there. List stems and open
  cells in module headers when a device pre-expands multiple forms.

## Validation

Run `./scripta/check-source` from this repo after source changes.
