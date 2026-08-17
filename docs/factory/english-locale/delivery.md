# Delivery — Norma English locale (english-locale goal): Pass A integrate + Pass B refresh

**Planner**: planner. **Assignment**: task `6e60ac86` (Mind, 2026-08-16).
**Goal**: [`goal.md`](goal.md) (`gol_ecc7b670a90073a2`; standalone, mirrors `gradus/docs/factory/english-locale/`).
**Repo**: norma (direct on main; radix read-only for locale-pack verification).
**Pass B detail spec**: [`pass-b-delivery.md`](pass-b-delivery.md) (refreshed in this wave; ledgers unchanged).
**Status**: **READY for delivery audit.** Pass A = INTEGRATE decision + 5 completion units + 1 recommended
pre-existing repair (RA-1). Pass B = 28 norma units + 1 radix-coordinated pack unit (PB-PACKS) = 29 units.

---

## 1. Goal-check summary (compact)

- **Goal path**: `norma/docs/factory/english-locale/goal.md`, Pass A (surface + locale) + Pass B (identifiers).
- **Evaluator mode**: goal-check → delivery lowering (task scope = reconcile + lower).
- **Intended consumer**: delivery audit → factory Hands (Pass A completion) → Pass B Hands.
- **Verdict**: **READY** with preconditions:
  (a) Pass A integrates first (unit PA-1) — every Pass B unit renames the post-A English surface, never Latin;
  (b) the toolchain oracle is the **rebuilt** `radix/target/debug/faber` (faber 1.7.0 from radix main,
  includes hand-6 71b0f6801 own-checkout-first pack resolution — the stale Aug-15 binary read `/tmp/radix`
  packs and misreported csv/valor as broken); (c) the scripta gate repair (PA-4) lands before any Pass B
  unit claims `./scripta/check-source` green (`faber script` is retired in 1.7.0); (d) §11 open questions
  (mechanical trio, async-twin, `nomen` escape) confirmed before the units that apply them (PB-CHORDA,
  PB-SOLUM, PB-TEMPUS, PB-JSON).
- **Reasoning**: Pass A commit `c006af5` sits only on `factory/hand-9`. It applies **cleanly** to current
  main (evidence in §2), and the post-A surface **strictly improves** every measured check surface over the
  la baseline with the correct toolchain (§3) — zero regressions. Pass B ledgers were re-verified against
  live `norma/src` (spot-checks on chorda/solum/valor/fila/tempus/json plus the full census; all names
  present, draft spellings correct). Remaining gaps are pre-existing and named, not campaign-caused.

## 2. Integrate-vs-relower decision: INTEGRATE

**Decision**: integrate `c006af5` as the first Pass A unit (PA-1). Do **not** re-lower Pass A as a fresh
convert pipeline (the gradus S1 mirror shape is unnecessary — the conversion is done, verified, and applies).

Evidence:

| Check | Result |
| --- | --- |
| Branch geometry | `factory/hand-9` = merge-base `d755ada` + `c006af5` (58 `.fab` files); main = `d755ada` + `a21d1b5` (AGENTS.md only, docs). |
| Clean application | `git merge-tree --write-tree main factory/hand-9` → clean tree, no conflicts. No file overlap between `a21d1b5` and `c006af5`. |
| Conversion surface | 34 `src/**/*.fab` + 24 `exempla/**/*.fab`: keyword/type/intrinsic surface la→en (`functio`→`fn`, `textus`→`string`, `varia`→`var`, `redde`→`return`, `dum`→`while`, `sectio`→`slice`, `longitudo`→`length`, `@ publica`→`@ public`, …), frontmatter `locale = "en"`, retired `privata` import marker removed (radix corpus was swept at the VM-U3 break; norma was missed). |
| Round-trip | Author-proven: 3 samples (arca, codex, toml) convert en→la back to byte-identical Latin originals. Re-verified in PA-5 on main. |
| Toolchain honesty | The commit's "csv/valor radix en-pack defect" was **resolved differently than filed**: export projection honors identity rows; the real cause was the dev-locale-pack CWD-ancestry bug, fixed by radix hand-6 `71b0f6801` (own-checkout-first). With a rebuilt binary, csv/valor check **clean** under en. No radix pack gap remains for norma. |
| Residuals (all pre-existing) | 5 parity src files (mathesis/fila/ordinata/vector/json/pange — identical error codes on la and en surfaces), caelum family visibility (improved), exempla visibility + parity (improved), package manifests (un-flipped), proba files (un-converted), scripta gates (broken by `faber script` retirement). Named in §3/§4. |

The conversion pipeline choice (`convert-corpus-locale.py` patch, not `faber convert --to en`) is recorded in
the commit message (`faber convert` was non-functional for the corpus: intrinsic surfaces stay canonical
Latin, type aliases expand, `private` re-emitted). Not re-litigated here.

## 3. Post-A measurement (honest, fresh binary, self-resolving library home)

Measured with the rebuilt `radix/target/debug/faber` (1.7.0 + hand-6), `FABER_LIBRARY_HOME` resolving
`norma:*` to the tree under test (a `/tmp` worktree resolves to the main checkout otherwise — that artifact
skews importing modules; caelum family numbers below are the honest ones).

| Surface | Main (la baseline, `--locale la`) | Post-A (`factory/hand-9`, default en) | Δ |
| --- | --- | --- | --- |
| `src/**/*.fab` (34) | 26 clean / 8 error (58 errs) | **27 clean / 7 error (29 errs)** | improved, **0 regressions** |
| `exempla` corpus `.fab` (22) | 0 clean / 22 error (all PARSE050 privata-marker + PARSE060) | **6 clean / 16 error** (SEM004/SEM006/SEM010 visibility + parity) | improved, **0 regressions** |
| package exempla (2) | crypta-sha2 1 err; ad-multiplica clean | crypta-sha2 49 err under en (SEM006/SEM010, pre-existing private-member calls); ad-multiplica clean | improved / parity |

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

## 4. Pass A unit graph (after the integrate decision)

**PA-1 — INTEGRATE `c006af5`** (first unit; the only mandatory Pass A unit)
- `write_scope`: norma only — bring `factory/hand-9`'s `c006af5` onto main (merge-commit or cherry-pick; `--ff-only` is impossible since main and `factory/hand-9` diverged at `d755ada`, so main is not an ancestor; both preserve the `factory/hand-9` pointer; byte-identical tree).
- `done_when`: main contains the 58-file conversion as one commit; `faber check` (default en, rebuilt binary) on `src/chorda.fab`, `src/csv.fab`, `src/valor.fab` exits 0; `git diff c006af5 main -- src exempla` empty.
- `depends_on`: none. `integrable`: yes (docs-only main since merge-base; clean merge-tree proven). `risk`: low.

**PA-2 — MANIFESTS: flip package exempla to `[locale] locale = "en"`**
- `write_scope`: `exempla/crypta-sha2/faber.toml`, `exempla/ad-multiplica-backward/faber.toml` (`[reader] locale = "la"` → canonical `[locale] locale = "en"`; legacy alias retired in the gradus pattern).
- `done_when`: `faber check exempla/ad-multiplica-backward` green (proven clean under `--locale en`); crypta-sha2 reduced to its named SEM006/SEM010 visibility residuals (R2 class — excluded per non-goals; report, don't fix here).
- `depends_on`: PA-1. `integrable`: yes. `risk`: low.

**PA-3 — PROBA: convert the 4 Latin-surfaced `.proba` files**
- `write_scope`: `src/mathesis.proba`, `exempla/caelum/terminus.proba`, `exempla/caelum/connexus.proba`, `exempla/caelum/auscultator.proba` (keyword surface `functio`→`fn`, `redde`→`return`, `fractus`→`float`, `fixum`→`const`, `forma(...)` constructor per the en surface, `importa ex`→`import from`; keep proba keywords and identifiers — those are Pass B).
- `done_when`: `faber test src/mathesis.proba` runs under default en with zero locale-class SEM008s (parity source errors in mathesis.fab may still surface — see RA-1); the 3 caelum proba check clean or match their module's known residual.
- `depends_on`: PA-1. `integrable`: yes. `risk`: low.
- Note: `.proba` files carry no frontmatter and were skipped by the corpus pipeline; `faber test` uses the default en pack, so the la-bodied proba are red post-A.

**PA-4 — GATES: repair `scripta/check-source` for the 1.7 toolchain**
- `write_scope`: `scripta/check-source` (retired `faber script` → `faber run`), `scripta/check-generated-output.fab` + `scripta/check-promoted-helper-imports.fab` (en frontmatter + Latin body — convert to the en surface so they parse under the en pack they declare).
- `done_when`: `FABER=<rebuilt radix/target/debug/faber> ./scripta/check-source` green on the integrated tree; the two `.fab` gates exit per their original semantics (generated-output rejects tracked `target/`; promoted-helper matrix validated).
- `depends_on`: PA-1. `integrable`: yes. `risk`: low.
- Note: `faber script` retired 2026-08-14 (`7412e5e55`), before 1.7.0; the Aug-9 migration (868b7e0) ran the gates under 1.6.0-rc.1's `script`. Every Pass B unit's `./scripta/check-source` done_when is blocked until this lands.

**PA-5 — VERIFY (goal row A2): pack completeness en↔la on main**
- `write_scope`: verification + ledger only: re-run the en→la round-trip proof from main (3 samples byte-identical), record the csv/valor resolution (hand-6, no pack gap), update `goal.md` ledger rows A1/A2 to done with the §3 measurement table.
- `done_when`: round-trip proof reproducible from main; goal ledger shows A1/A2 done; no radix pack gap filed.
- `depends_on`: PA-1. `integrable`: yes. `risk`: low.

**RA-1 — (recommended) PARITY: repair the 5 pre-existing src error files** — mathesis, fila, ordinata, vector, json/pange. *Not caused by Pass A* (identical errors on the la baseline); **precondition** for the full-green Pass B done_when on PB-MATHESIS/PB-FILA/PB-ORDINATA/PB-VECTOR/PB-JSON (`faber check src/<m>.fab` exit 0). Dispatch decision → Mind: land before the affected Pass B units, or amend those units' done_when to "no new errors; parity unchanged" and file a separate hygiene wave.

**Residual visibility (Pass A excluded this; lowered 2026-08-17):** caelum family SEM006, exempla SEM004/SEM006 private-member, crypta-sha2 SEM006/SEM010 — Hand units in [`visibility-residual-delivery.md`](visibility-residual-delivery.md) (task `03c9dd34`; want `390d295c`). Not a reopen of Pass A/B.

## 5. Pass B (refreshed)

- Detailed per-module ledgers, unit specs, merge gate, and open questions: [`pass-b-delivery.md`](pass-b-delivery.md).
- **Count corrected**: 29 units (draft said 27 — miscount). 28 norma units (F1 3, F2 1, F3 22, F4 1, F6 1) + 1 radix-coordinated pack unit (F5 PB-PACKS).
- **Preconditions** (updated vs the draft): PA-1 through PA-4 land first (integrate, manifests, proba, gates); FABER_BIN is the rebuilt binary; `./scripta/check-source` green only after PA-4.
- **Ledgers verified live** in this wave: census holds (34 src `.fab` + 1 proba; 22 exempla corpus `.fab` + 3 proba; 2 packages); chorda/solum/valor/fila/tempus/json ledger names all present in live src; `renomina` spelling confirmed.
- **RA-1 interplay**: the 5 parity modules keep pre-existing errors under Pass B renames unless RA-1 lands first (see §4).
- PB-PACKS (radix side) still required in the same merge wave as PB-CHORDA — the en pack defines `norma:chorda` as a **total identity** (9 rows, verified live in `radix/stdlib/locale/en/pack.toml`); the la pack has 0 rows, so la readers would read English canonicals after renames until rows land.

## 6. Lane-owned validation (named once)

- **Lint lane**: stages 1–2 on the integrated tree (renames are mechanical; `git diff --check`).
- **Test lane**: `faber check` per touched module (default en pack), `faber test` on proba, `./scripta/check-source` (post-PA-4), exempla corpus + packages after PB-CONSUMERS.
- **Merge lane**: PA-1 lands `factory/hand-9` onto main; Pass B families land in §6 order of pass-b-delivery.md; PB-CONSUMERS batches with the final family; PB-PACKS (radix) coordinates with PB-CHORDA; `scripta/verify-main-consistent` per repo.
- **Toolchain contract**: FABER_BIN = rebuilt `radix/target/debug/faber` (must postdate hand-6 71b0f6801); FABER_LIBRARY_HOME must resolve `norma:*` to the tree under test (packet root in lane mode; the main checkout in direct mode) — a `/tmp` worktree check silently reads the main checkout for imports.

## 7. Open questions for Mind

1. RA-1 dispatch: land the 5 parity repairs before the affected Pass B units, or amend those done_whens and file a separate hygiene wave? (Default: land first — keeps Pass B's `faber check` oracle honest.)
2. SEM006 visibility follow-up — **answered 2026-08-17**: lowered as residual delivery [`visibility-residual-delivery.md`](visibility-residual-delivery.md) (task `03c9dd34`). Do not reopen Pass A/B; do not start C1 from that spec.
3. FABER_BIN rebuild: the in-tree binary was stale (Aug-15, pre-hand-6). Confirm the rebuilt binary (or a lane packet dev build) is the verified oracle for Hands.
4. `faber test` surface: norma root has no `faber.toml`; per-file `faber test` needs `--locale`/package context. Should Pass A add a root package manifest, or is per-file `faber check` + `faber test <file>` the standing oracle? (Default: no new manifest; per-file.)
5. Carry-forward from pass-b-delivery.md §10 (mechanical trio, async-twin, `nomen` escape, pack-currency split, external-caller follow-up, `model.fab` pre-existing breakage, packet staleness).
