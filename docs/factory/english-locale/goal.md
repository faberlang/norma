# GOAL: Norma English locale + English identifiers

**Status**: active — Pass A conversion commit c006af5 on factory/hand-9; integrate decision INTEGRATE (delivery.md, PA-1..PA-5); Pass B lowered (pass-b-delivery.md, 29 units)
**Created**: 2026-08-15
**Campaign:** `—` (standalone; mirrors `gradus/docs/factory/english-locale/`)
**Source:** operator request 2026-08-15 (batch 2) — same conversion as gradus
**Repos:** norma (primary); radix read-only for locale-pack verification
**Related:** gradus english-locale goal (pattern authority), memo 86f92441 (pack principle), memo 2710cb44 (operator batch)

---

## Invariant

Norma converts from the Latin reader surface to English with **both en and la locale packs complete**, so a Latin reader consuming norma loses nothing — packs carry the translation; native surface is maintainer ergonomics. Import coordinates (`norma:*`) stay. Pre-1.0 clean break, no shims.

## Problem

- 26/26 `src/*.fab` files carry `locale = "la"` frontmatter with Latin keyword surface (13+ verified untagged-body Latin in exempla); `faber.toml`-level locale if present follows the gradus pattern.
- Norma exempla + stdlib instruction corpus ride the same surface; radix `stdlib/locale/la/` contains norma exemplars (the `salve-munde` incident showed this coupling).
- User identifiers are Latin (`causa`, `valor`, etc.) and are not pack rows — Pass B renames them.

## Proposal

Mirror the gradus english-locale two-pass shape:

- **Pass A — surface + locale**: `faber convert <file> --from la --to en` over `norma/src` + exempla `.fab`; flip manifests to `locale = "en"`; verify locale-pack completeness BOTH directions: an English-converted file converted `--to la` reproduces the Latin keyword surface (round-trip proof used in gradus). **Pack completeness is acceptance, not afterthought** — if en↔la packs are missing rows the conversion exercises, that is a radix locale-pack gap filed back (radix read-only for norma work).
- **Pass B — identifiers**: planner-lowered per-module rename ledgers (live census, seed conventions from gradus: reserved-word escapes like `valor→payload`, `causa→message`), semantic-error-free per landing (`faber test .` green each unit), `norma:*` coordinates unchanged.
- **Proba coverage workstream** rides after Pass A (operator batch item 2): `.proba` tests per module in faber-native format, `faber test` as runner; faber-command weaknesses surfaced become radix fix units + a dev build used for verification.

### Non-goals

- No radix product changes except locale-pack gaps and `faber test` fixes the work surfaces.
- No schema/API redesign; renames are mechanical.

## Units (lowering via `$delivery` after Pass A survey)

| Unit | Scope | Depends | Hand evidence |
| --- | --- | --- | --- |
| A1 | convert `norma/src` + exempla; manifest flips; round-trip proof | — | — |
| A2 | locale-pack completeness verification (en+la), file radix gaps | A1 | — |
| B1+ | identifier renames per module (planner-lowered) | A1, A2 | — |
| C1+ | proba coverage per module + faber-test fixes + dev-build verify | A1 | — |
| R1 | formal tagged release (release lane v2, notes pre-written) | B, C complete | — |

## Validation

Round-trip locale proof both directions; `faber test .` green per package post-conversion and post-rename; proba suite green under the dev build; tagged release executes mechanically with pre-written notes.

## Ledger

| Unit | Status | Hand | Receipt | Notes |
| --- | --- | --- | --- | --- |
| all | pending | — | — | operator batch 2; autonomous mode |

## Open questions

1. Do exempla frontmatter tags match src conventions after conversion (audit tool alignment)? Default: yes, audit must stay green.
2. Proba scope floor: one suite per module vs. per-public-function? Default: per-module with named cases for public API.
3. Release versioning scheme for previously-unversioned libs (0.1.0 vs 1.0.0)? Default: 0.2.0 matching manifest current, operator may re-tag.
