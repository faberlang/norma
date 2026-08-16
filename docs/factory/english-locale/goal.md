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
| A1 | done | hand-9 (PA-1) | `aad0d6a` (carries `c006af5`) | src (34) + exempla (24) la→en conversion, frontmatter en, `privata` marker removed; round-trip proof 3/3 byte-identical (arca, codex, toml) |
| A2 | done | hand `7f3aa5ab` (PA-5) | `7f3aa5ab` | pack completeness verified on main: csv/valor/chorda check clean under en with rebuilt binary (hand-6 `71b0f6801` own-checkout-first; no radix pack gap); §3 measurement re-verified below |
| B1+ | pending | — | — | identifier renames per module (pass-b-delivery.md) |
| C1+ | pending | — | — | proba coverage + faber-test fixes |
| R1 | pending | — | — | formal tagged release |

### §3 Pass A measurement (post-A column re-verified on main)

Measured with the rebuilt `radix/target/debug/faber` (1.7.0 + hand-6 `71b0f6801`), `FABER_LIBRARY_HOME` resolving `norma:*` to the main checkout.

| Surface | Main (la baseline, `--locale la`) | Post-A (main, default en) | Δ |
| --- | --- | --- | --- |
| `src/**/*.fab` (34) | 26 clean / 8 error (58 errs) | **27 clean / 7 error (29 errs)** | improved, **0 regressions** |
| `exempla` corpus `.fab` (22) | 0 clean / 22 error (all PARSE050 privata-marker + PARSE060) | **6 clean / 16 error** (SEM004/SEM006/SEM010 visibility + parity) | improved, **0 regressions** |
| package exempla (2) | crypta-sha2 1 err; ad-multiplica clean | crypta-sha2 49 err under en (SEM006×16, SEM010×33, pre-existing private-member calls); ad-multiplica clean | improved / parity |

**Post-A src residual files** (7; all pre-existing):

| File | Errors | Class |
| --- | --- | --- |
| `src/mathesis.fab` | 15 (SEM010×10, SEM011×5) | parity — identical on la baseline |
| `src/fila.fab` | 2 (PARSE001, PARSE030) | parity — identical |
| `src/ordinata.fab` | 3 (PARSE030×3) | parity — identical |
| `src/vector.fab` | 3 (SEM010×2, SEM014) | parity — identical |
| `src/json/pange.fab` | 3 (SEM010×2, SEM016) | parity — identical |
| `src/caelum.fab` | 1 (SEM006) | visibility — was 22 PARSE errors on la; improved |
| `src/caelum/auscultator.fab` | 2 (SEM006) | visibility — was 5; improved |

`src/caelum/connexus.fab`: 5 la-baseline errors → **0 post-A** (fixed by the privata-marker removal).

**Round-trip oracle (PA-5 re-verified on main).** Byte-identity holds via the corpus locale pipeline reverse (`radix/scripta/convert-corpus-locale.py --from-locale en --to-locale la`): arca/codex/toml each reproduce the `d755ada` Latin original byte-identically, and the la→en direction reproduces the main en files — an exact bijection. `faber convert --to la` converts the keyword/type surface but is a normalizing emitter (drops section comments, expands type aliases `series`→`tabula<textus, valor>` / `cursor`→`cursor_t`, emits analytical `fiet`, normalizes `@ futura`→`@ futura { }`, does not rewrite frontmatter or reverse identifier identity rows) — consistent with the c006af5 commit note; it is the keyword-surface oracle, not the byte-identity oracle.

## Open questions

1. Do exempla frontmatter tags match src conventions after conversion (audit tool alignment)? Default: yes, audit must stay green.
2. Proba scope floor: one suite per module vs. per-public-function? Default: per-module with named cases for public API.
3. Release versioning scheme for previously-unversioned libs (0.1.0 vs 1.0.0)? Default: 0.2.0 matching manifest current, operator may re-tag.
