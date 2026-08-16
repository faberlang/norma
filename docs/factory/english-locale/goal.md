# GOAL: Norma English locale + English identifiers

**Status**: done — Pass A (PA-1..PA-5) + RA-1 + Pass B (F1-F6, 29 units incl. amendments) landed 2026-08-16; C1+/R1 deferred (residual, non-blocking); residual debt recorded in §Residuals
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
| A1 | convert `norma/src` + exempla; manifest flips; round-trip proof | — | `aad0d6a` (PA-1) |
| A2 | locale-pack completeness verification (en+la), file radix gaps | A1 | `03e612b` (PA-5) |
| B1+ | identifier renames per module (planner-lowered) | A1, A2 | 28 units — see Ledger |
| C1+ | proba coverage per module + faber-test fixes + dev-build verify | A1 | deferred — residual (see §Residuals) |
| R1 | formal tagged release (release lane v2, notes pre-written) | B, C complete | deferred — residual (see §Residuals) |

## Validation

Round-trip locale proof both directions; `faber test .` green per package post-conversion and post-rename; proba suite green under the dev build; tagged release executes mechanically with pre-written notes.

## Ledger

| Unit | Status | Hand | Receipt | Notes |
| --- | --- | --- | --- | --- |
| A1 | done | hand-9 (PA-1) | `aad0d6a` (carries `c006af5`) | src (34) + exempla (24) la→en conversion, frontmatter en, `privata` marker removed; round-trip proof 3/3 byte-identical (arca, codex, toml) |
| A2 | done | hand `7f3aa5ab` (PA-5) | `03e612b` | pack completeness verified on main: csv/valor/chorda check clean under en with rebuilt binary (hand-6 `71b0f6801` own-checkout-first; no radix pack gap); §3 measurement re-verified below |
| B1+ | done | Pass B hands (F1-F6) | 28 units — see Pass B receipts below | identifier renames per module (pass-b-delivery.md); all 29 Pass B units landed incl. radix PB-PACKS |
| C1+ | deferred | — | — | proba coverage + faber-test fixes — residual, non-blocking (see §Residuals) |
| R1 | deferred | — | — | formal tagged release — residual, non-blocking (see §Residuals) |

### Pass B receipts (F1-F6, 29 units = 28 norma + 1 radix PB-PACKS)

| Unit | Receipt |
| --- | --- |
| PB-CHORDA / PB-CSV / PB-VALOR (F1) | `f937469` / `5e8a764` / `3a76c9f` |
| PB-CAELUM (F2) | `0fe4182` |
| PB-ALEATOR / PB-ARCA / PB-CODEX / PB-CONSOLUM / PB-CRYPTA / PB-FILA / PB-HTTP / PB-JSON / PB-MATHESIS / PB-MODEL / PB-NUNCIUS / PB-ORDINATA / PB-PRESSURA / PB-PROCESSUS / PB-SOLUM / PB-TEMPUS / PB-TENSOR / PB-THESAURUS / PB-TOML / PB-VECTOR / PB-YAML (F3, 21 commits) | `aa84940` `171800d` `df7d9b5` `0812e5d` `2e116f7` `a039855` `0e65668` `13cdd13` `e638b2e` `983dab6` `a19f4e8` `c0bd560` `e930361` `4667682` `2b3cb19` `774df57` `0d17665` `138923b` `582a563` `66a5801` `a49a643` |
| PB-OPTIMIZER (F3, verify-only) | no commit — already English, 0 renames |
| PB-CONSUMERS (F4) | `126580c` |
| PB-PACKS (F5, radix-coordinated) | radix `deef3cc72` + `45c828b10` (en/la pack rows; la pack now 87 `norma` rows) |
| PB-DOCS (F6) | `4ed6e71` |
| Amendments | `3914016` (RA-1 ledger amend), `225517f` (PB-HTTP ledger amend), `f7a5cc8` (param-policy amend) |
| RA-1 parity repair | `d3d5931` |
| la-surface export restore | `0d7b8e0` |

### Pass B ledger notes

- **F6 fold-in (2026-08-16, PB-DOCS):** PB-SOLUM async-twin la-pack gap — checked against the §4.5
  external-caller census: no la-locale consumer calls the async twins (`read_file_async`,
  `write_file_async`, `read_bytes_async`, …). La consumers (examples/coreutils, vivilite,
  ai-workbench) use sync spellings only; the only async-twin call sites (`solum.leget`,
  `tempus.dormiet`) are en-surface radix/corpus files. The la pack therefore needs no async-twin
  rows for PB-SOLUM (PB-PACKS covers the sync renames); gap recorded in the ledger notes, no rows
  added.

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

## Residuals (recorded at closeout; do not block done)

All residual classes below are pre-existing / baseline per the delivery
(`delivery.md` §3-§4, `pass-b-delivery.md` §4.6) — none are Pass A/B
regressions. Named so the closeout is honest and the follow-up has a home.

| Residual | Class / detail | Wanted fix |
| --- | --- | --- |
| SEM006/SEM004 visibility residuals | `src/caelum.fab` (1×SEM006), `src/caelum/auscultator.fab` (2×SEM006), exempla SEM004/SEM006 private-member violations, crypta-sha2 SEM006×16 — visibility-model class, pre-existing | want `390d295c` — precondition now met, **undispatched** |
| Stale verb headers | `src/**` module headers still quote Latin verb naming prose ("Latin stems chosen for motion…", "Verbs route over stream `ad` (Latin `solum:*` routes)…") — F6/comment-pass territory, members already en | want `db928be6` |
| json la-row currency check | la-pack `norma:json` rows were added in PB-PACKS but need a currency re-check against the landed json renames | want `bb21dc95` class |
| crypta-sha2 49-err baseline | package exemplum red under en: SEM006×16 + SEM010×33 pre-existing private-member calls (see §3 measurement table) | baseline — visibility-model class, same as first row |

**Deferred units (recorded, not blocking):**

- **C1+** proba coverage per module + faber-test fixes + dev-build verify — never
  lowered in this wave (PA-3 converted the 4 Latin-surfaced `.proba` files; the
  per-module coverage workstream remains). The `src/mathesis.proba` colocated
  proof and the 3 caelum exempla proba are the shipped proba surface.
- **R1** formal tagged release — norma has no standalone release process
  (workspace release table: source repo, no standalone release); nothing was
  tagged at closeout.

## Open questions

1. Do exempla frontmatter tags match src conventions after conversion (audit tool alignment)? Default: yes, audit must stay green.
2. Proba scope floor: one suite per module vs. per-public-function? Default: per-module with named cases for public API.
3. Release versioning scheme for previously-unversioned libs (0.1.0 vs 1.0.0)? Default: 0.2.0 matching manifest current, operator may re-tag.
