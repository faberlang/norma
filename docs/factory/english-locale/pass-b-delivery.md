# Delivery Lowering — Norma Pass B: English identifier renames (english-locale goal, unit B1+)

**Planner**: planner-2 (authored) / planner (refreshed 2026-08-16, task `6e60ac86`).
**Goal**: [`goal.md`](goal.md). **Campaign**: standalone (mirrors `gradus/docs/factory/english-locale/`).
**Pattern authority**: `gradus/docs/factory/english-locale/pass-b-delivery.md` (planner-1, task 49a3ac9c).
**Master delivery (this wave)**: [`delivery.md`](delivery.md) — Pass A integrate decision + Pass A units.
**Status**: **REFRESHED — READY to dispatch** once Pass A lands on main (PA-1 integrate + PA-2 manifests +
PA-3 proba + PA-4 scripta gates, per `delivery.md`) and Mind confirms the §11 open questions before the
first unit that applies them (PB-CHORDA applies the mechanical-trio and async-twin conventions).

> Refresh notes (2026-08-16): ledgers re-verified against live `norma/src` (census + spot-checks on
> chorda/solum/valor/fila/tempus/json — all ledger names present; `renomina` spelling confirmed). Unit
> count corrected from 27 to **29** (28 norma units + 1 radix-coordinated PB-PACKS). Census corrected:
> exempla corpus = **25 files** (22 `.fab` + 3 `.proba`), not 23. Toolchain oracle updated to the rebuilt
> faber 1.7.0 binary (§4.6).

> RA-1 amendment (2026-08-16, task 69381782): ledgers amended for the RA-1 parity repair `d3d5931`
> (5 modules now compile). **ordinata**: `fines` and `_insere_ad` removed (generic `<` ordering
> unavailable); PB-ORDINATA rows `fines`→`keys_between` and `_insere_ad`→`_insert_at` struck; RA-1-added
> privata helper `_aequa`→`_equals` added. **vector**: generic `swizzle` wrapper removed (result width =
> pattern length, unresolvable for size N — SEM014); PB-VECTOR "Keep: `swizzle`" dropped and `swizzle`
> removed from the §5 rule-5 list. **constructors**: fila/ordinata free constructors now declare their
> own type params (`creata<T>()`, `creata_ex<K, V>(…)`) with type-args-inferred `Fila { }`/`Ordinata { }`
> bodies — call sites must use turbofish; PB-FILA/PB-ORDINATA/PB-CONSUMERS notes updated.

---

## 1. Goal-check summary (compact)

- **Goal path**: `norma/docs/factory/english-locale/goal.md` (Pass B), unit row `B1+`.
- **Evaluator mode**: delivery lowering (goal is `READY`-shaped — Pass A structure settled, identifier census below).
- **Intended consumer**: delivery → factory Hands (Pass B units).
- **Verdict**: **READY** with preconditions: (a) Pass A lands on main first — **integrate decision is INTEGRATE**
  (`c006af5` applies cleanly; see `delivery.md` §2) — Pass B renames English-surface files, never Latin;
  (b) the §11 open questions (mechanical-trio English, async-twin `_async` suffix, `nomen`→`file_name` escape)
  are confirmed before PB-CHORDA applies them; (c) the la-pack currency unit (PB-PACKS) is filed back to radix
  in the same wave as the first renames, or la-locale consumers read the new English canonicals directly
  (see §4 pack interplay and §5 rule 9); (d) `./scripta/check-source` is only green **after** the PA-4 gate
  repair (`faber script` → `faber run`, faber 1.7.0); (e) **RA-1 parity repair** (delivery.md §4) lands before
  PB-MATHESIS/PB-FILA/PB-ORDINATA/PB-VECTOR/PB-JSON, or those units' done_when is amended to "no new errors;
  parity unchanged" — those five modules carry pre-existing errors identical on the la baseline.
- **Reasoning**: every Pass B rename is mechanical against the locked ledgers in §7; the per-module surface is
  fully enumerated here (§4) from the live tree (re-verified 2026-08-16); each unit fixes every in-repo
  reference to the names it renames, so the norma package (`faber test .` / `./scripta/check-source`) stays
  green after each landing; reserved-name escapes and the locked conventions close collision risk; external
  callers (examples/radix/faberlang.dev/hosts) are named findings (§4.5), not this goal's completion contract.

## 2. Interpreted theme

Norma ships a Latin *identifier* surface (functions, genus/implendum types, fields, params) that Pass A's
locale conversion does not move. Pass B converts that surface to English, per-module, on the **post-Pass-A
English-surface files** (`locale = "en"` frontmatter + en keyword/type/intrinsic spellings). Import
coordinates (`norma:chorda`, …) stay. Pre-1.0 clean break, no shims, no forwarding aliases.

## 3. Normalized spec (delivery-sized outcome)

After Pass B, in the norma repo:

1. Every user identifier in `src/**/*.fab`, `src/**/*.proba`, and the exempla corpus (`exempla/**`) is
   English per the locked ledgers.
2. Reserved-word escapes honored: no new fn/type/method collides with en keywords or types (`read`,
   `write`, `warn`, `debug`, `print`, `name`, `size`, `value`, `args`, `require`, `range`, `between`,
   `set`, `map`, `filter`, … are en keywords/types — see §5 rule 8).
3. `norma:*` import coordinates and module aliases unchanged; only member spellings move.
4. en/la locale-pack `[[library_members]]` rows track the new canonicals (PB-PACKS, radix-side filed gap):
   the en pack's `norma:chorda` rows (canonical `retorta`→surface `reverse`, identity rows) are rewritten
   to the new English canonicals, and the la pack gains rows so la readers still read `chorda.retorta`,
   `solum.lege`, etc. (goal invariant: "a Latin reader consuming norma loses nothing").
5. `scripta/check-source` carries Pass B Latin-identifier guards; audit greps stay green.
6. External consumers (`examples/*`, `radix/corpus/*`, `faberlang.dev`, `hosts`) are **NOT** touched here —
   named findings (§4.5) and a follow-up, consistent with the goal's "radix/examples OUT of scope per
   import-coordinate stability".

## 4. Repo-aware baseline (measured against norma main, read-only)

- Pass A commit `c006af5` exists **only on `factory/hand-9`** (1 ahead of merge-base `d755ada`; main is
  +1 docs-only `a21d1b5`). **Integrate decision: INTEGRATE** (clean merge-tree, zero regressions — full
  evidence in `delivery.md` §2–§3). Until PA-1 lands, all 34 `src/**/*.fab` on main still carry
  `locale = "la"`; exempla corpus files carry `locale = "la"` frontmatter or la keyword surface; the two
  package exempla (`exempla/crypta-sha2`, `exempla/ad-multiplica-backward`) have `[reader] locale = "la"`
  (flipped by PA-2).
- Module surface (live, pre-Pass-A spellings — identifiers do not move in Pass A): **34 src files**
  (26 top-level + `caelum/*` 3 + `json/*` 4 + `solum/path`), **1 colocated proba** (`src/mathesis.proba`),
  **22 exempla corpus `.fab` files** (`exempla/stdlib-nativum/*` 17, `exempla/chorda/*` 5), **3 exempla
  `.proba` files** (`exempla/caelum/*.proba`), **2 package exempla** (crypta-sha2, ad-multiplica-backward).
  Full identifier census per module in §7.
- Import DAG (measured, for ordering): `chorda` is a leaf imported by `csv` and `valor`; `caelum` imports
  `caelum/terminus`, `caelum/connexus`, `caelum/auscultator`; `caelum/connexus` imports `caelum/terminus`;
  `caelum/auscultator` imports `caelum/connexus`; all other modules are leaves with no in-src importers.
- Validation oracle (post-Pass-A): per-file `faber check src/<m>.fab` exits 0 under the **default en pack**
  (single-file check does not select a pack from frontmatter; the la baseline is measured with
  `--locale la`); `faber test` on proba per file or via a package path; `./scripta/check-source` green
  (after PA-4). See §4.6 for the toolchain contract.
- **Pack interplay (measured)**: the en pack (`radix/stdlib/locale/en/pack.toml`) defines `[[library_members]]`
  for `norma:chorda` as a **total identity** — rows `retorta→reverse`, `nexa→nexa`, `retenta_iudicio→…`,
  `retenta`, `expurgata`, `diducta`, `discissa`, `pange`, `solve` (identity rows keep the canonical spelling).
  `strict_library_members` means a non-la pack that defines an identity is total: unmapped members are not
  installed and canonical aliases are not accepted. **Therefore Pass B renames require coordinated en/la pack
  row updates (PB-PACKS), or en readers of `norma:chorda` lose every member** (canonical names change under a
  total identity). The la pack currently has **zero** `[[library_members]]` rows, so la readers resolve
  canonical names directly; after Pass B renames, la readers would read English unless the la pack gains rows.
  PB-PACKS is the mechanism that keeps the goal's "Latin reader loses nothing" invariant.
  (Verified live 2026-08-16: 9 `package = "norma"` rows in the en pack; 0 rows in the la pack.)

### 4.5 External-caller census (findings — OUT of scope for Pass B Hands)

Every external consumer imports by **module coordinate** (`importa ex "norma:solum" privata solum`) and calls
**members** by name. Coordinates survive Pass B; member spellings move.

| Consumer | Members used (would break if renamed without la-pack rows) |
| --- | --- |
| `examples/coreutils/packages/*` (15 packages, `locale = "la"`) | `solum.carpe`, `solum.dele`, `solum.amputa`, `solum.tange`, `solum.sequere`, `solum.scribe`, `solum.absolve`, `solum.renomina`, `solum.exscribe`, `solum.crea`, `solum.exstat`, `consolum.dic`, `consolum.mone`, `processus.sedes`, `processus.lege` |
| `examples/vivilite` (la) | `solum.exstat`, `solum.scribe`, `json.pange`, `solum.crea`, `solum.appone`, `solum.temporarium`, `solum.carpe` |
| `examples/ai-workbench/packages/faber-ai` (la) | `solum.exstat/regularene/legibilene/dele/carpe/scribe/lege/partem/parens/mensura/inveni`, `json.pange/solve`, `processus.captura`, `model.formatum`, `model.diagnosticum` (also calls `model.tempta_safetensors_summarium`, `model.gguf_quantization_fragmentum`, `model.tempta_gguf_summarium` — **pre-existing breakage**, not in current `model.fab`) |
| `radix/corpus/*` (en-surface: `async-solum-leget.fab`, `solum-lege-generic.fab`, `async-tempus-dormiet.fab`, `instans.fab`, `json.fab`) | `solum.lege/leget/scribe`, `tempus.nunc/expectet/dormiet`, `toml.solve`, `valor.cape`, `json.pange/solve/tempta` |
| `radix/crates/radix/tests/fixtures/hir_artifact/library-import.fab`, `tree-sitter-faber/fixtures/corpus-derived.fab` | import-only (`norma:chorda`, `norma:solum`, `norma:json`) — no member calls, **safe** |
| `faberlang.dev/generator/src/main.fab` | `solum.lege` (en); `locale-tabs/*` reference retired `norma:textus` — stale fixtures |
| `hosts/llvm/exempla/tensor/llvm-placement-v1.fab` | `tensor.structa/applicata/planata` |

**Census verdict**: renames break these call sites at the **member** level (coordinates stay, so imports stay).
For **la-locale** consumers (examples/*), la-pack `[[library_members]]` rows (PB-PACKS, radix) preserve the
Latin spellings, so they do **not** need code edits. **en-locale** consumers (radix/corpus, faberlang.dev,
hosts) would need member-name updates — named follow-up, NOT this goal's completion contract (report to Mind).

### 4.6 Toolchain contract (faber 1.7.0 — measured)

- `FABER_BIN` = **rebuilt** `radix/target/debug/faber` (must postdate radix hand-6 `71b0f6801`,
  2026-08-15 16:01:49 -0400). The pre-rebuild Aug-15 binary walked CWD ancestry for the dev locale pack and read a
  stale `/tmp/radix` checkout, misreporting csv/valor as en-pack-broken. The fixed binary resolves the exe
  tree first; the in-tree binary was rebuilt for this delivery and re-verified.
- `FABER_LIBRARY_HOME` must resolve `norma:*` to the **tree under test** (packet root in lane mode; main
  checkout in direct mode). Checking an importing module from a `/tmp` worktree silently resolves imports
  to the main checkout (observed: caelum family PKG001/SEM006 artifacts).
- Single-file `faber check` uses the default **en** pack; file frontmatter does not select the pack. The la
  baseline is measured with `--locale la`.
- `./scripta/check-source` currently calls the retired `faber script` and its two `.fab` gates carry en
  frontmatter with Latin bodies — broken under 1.7.0 until the PA-4 gate repair lands.
- `faber test` per `.proba` uses the default en pack; the 4 la-bodied proba files are red until PA-3
  converts them. The norma root has no `faber.toml` (locale comes from file frontmatter); there is no
  root-package `faber test .` surface.
- **RA-1 precondition**: `src/mathesis.fab`, `src/fila.fab`, `src/ordinata.fab`, `src/vector.fab`,
  `src/json/pange.fab` carry pre-existing errors identical on the la and en surfaces (delivery.md §3).
  PB-MATHESIS / PB-FILA / PB-ORDINATA / PB-VECTOR / PB-JSON done_when (`faber check` exit 0) is blocked
  until RA-1 lands or is amended per delivery.md §4.

## 5. Rename authority: seed + locked conventions

1. **Reserved — never rename TO** (en pack `[keywords]` + `[types]`; exact spellings are illegal):
   keywords `fn class union enum type const let var import from as public private optional return if else
   elif then for while match case break continue throw catch do assert panic true false null and or not is
   self main print test read write warn debug size name step range between within until line args call
   await async future format require exit`; types `string int bool float list map set bytes value void
   unknown null_ty instant json object any promise iterator tensor vector matrix ascii`; annotation keys
   (`@ name`, `@ future`, `@ cursor`, …); compiler-owned builtin frame-type variants `request item byte bulk
   done error cancel` (registered whenever frame types are needed — `STATUS_VARIANTS` in
   `radix/crates/radix/src/builtins/frame_types.rs`; a fn named after one compiles bare but `+ call` = SEM005,
   e.g. `fn request`). Compound escapes (`read_file`, `is_null`, `write_bytes`) are legal
   (exact-match lexing); `is_*` follows the seed's `est_*`→`is_*` precedent.
2. **Locked shared verbs** (gradus seed where the same stem appears, extended for norma):
   `accipe`→`get` (map/semaphore contexts) or `accept` (listener context); `appende`→`append`;
   `inveni`→`find`; `claude`→`close`; `nomen`→`file_name` (path basename — `name` is an en annotation key);
   `clavis`→`key`; `via`→`path`; `longitudo`→`length`; `vacua`→`is_empty`; `mensura`→`byte_length`;
   `modus`→`mode` (status/mode) or `method` (http request verb); `structa`→`construct`; `creata`/`creata_ex`→
   `empty`/`from_list` (fila) and `empty`/`from_map` (ordinata); `est_*`/`*ne` predicates→`is_*`;
   `verifica`→`verify`; `lege/leget`→`read_file`/`read_file_async` (solum), `read_line` (consolum),
   `read_env` (processus), `read_value` (semaphore) — bare `read` is an en keyword; `scribe/scribet`→
   `write_file`/`write_line`/`write_env`; `hauri/hauriet`→`read_bytes`; `funde/fundet`→`write_bytes`;
   `carpe/carpiet`→`read_lines`; `dic/dicet`→`write_partial`; `mone/monet`→`warn_line`; `vide/videbit`→
   `debug_line` (bare `write`/`warn`/`debug`/`print` are en keywords); `dele/delet`→`delete`;
   `exstat/exstabit`→`exists`; `clavis`→`key`; nominal state fields per module (`modus`→`mode`,
   `mensura`→`byte_length`, `directoriumne`→`is_directory`, `hospes`→`host`, …).
3. **Locked mechanical trio** (§11 Q1 default): `pange`→`encode` (byte codec, chorda) / `stringify`
   (value↔text codecs: json/toml/yaml/csv); `solve`→`decode` (chorda) / `parse` (json/toml/yaml/csv/tempus) /
   `decompress` (pressura) / `unlock` (nuncius Mutex); `tempta`→`try_decode` (chorda) / `try_parse`
   (json/toml/yaml) / `try_lock` (Mutex) / `try_wait` (Semaphorum). Confirm at dispatch (§11 Q1).
4. **Async twins** (§11 Q2 default): sync gets the plain English verb; the `@ futura` twin gets the same
   verb + `_async` suffix (`read_file`/`read_file_async`, `write_bytes`/`write_bytes_async`). Confirm.
5. **Already English — do not touch**: `listen accept respond stop status param ttl uuid sgd_step`,
   `PI E TAU`, `_sha256 _sha512_engine _K32` crypto internals, technical quant/dtype spellings, already-
   English params (`data`, `url`, `path`, `index`, `handle`, `args`). Pass B is not a style pass.
6. **Pack vocabulary is Pass A's surface, not Pass B's**: keywords, types, intrinsics (`functio→fn`,
   `valor→value`, `accipe→get`, `appende→append`, `longitudo→length`, `vacua→is_empty`, `ordina→sort`,
   …), `@ publica`/`@ privata`→`@ public`/`@ private`. Hands must not rename these; they are already en on
   the post-A surface.
7. **Privata `_`-prefixed helpers are included** in the rename surface (clean break leaves no Latin
   identifiers in `src/`); they are exempt from any api-reference-style coverage gate (norma has none).
8. **Probe-first (each unit, on the first converted file of the module):**
   ```bash
   "$FABER_BIN" check src/<m>.fab     # post-A: package locale is en, no --locale en
   ```
   Extra probes when the ledger introduces `fn file_name`-style names, `@ future`/`@ cursor` annotations,
   or a name whose en spelling is a keyword suffix (`read_bytes`).
9. **Pack currency**: every unit that renames **public** members records the old→new canonical pairs in
   its completion note; PB-PACKS (§8) consumes the union so en/la pack rows stay total and la readers
   lose nothing. The first rename unit (PB-CHORDA) MUST NOT land on main without PB-PACKS in the same
   merge wave (en pack defines `norma:chorda` as total — see §4).

## 6. Unit graph (ordered)

Pass B begins only after Pass A lands (PA-1 integrate + PA-2 manifests + PA-3 proba + PA-4 scripta gates;
see `delivery.md` §4). Units are **serialized only where caller files overlap**; all other units are
caller-disjoint leaves and may be dispatched in parallel (⚡).

```
Base: A1 (Pass A convert) → A2 (pack completeness + manifest flips)         [Pass A]
F1  PB-CHORDA → PB-CSV, PB-VALOR        (chorda renamed first; csv/valor files reference chorda members)
F2  PB-CAELUM                           (one unit: caelum.fab + terminus/connexus/auscultator, internal chain)
F3  PB-ALEATOR ⚡ · PB-ARCA ⚡ · PB-CODEX ⚡ · PB-CONSOLUM ⚡ · PB-CRYPTA ⚡ · PB-FILA ⚡ · PB-HTTP ⚡ ·
    PB-JSON ⚡ (json.fab + json/pange + json/solve + json/lexica + json/cursor) · PB-MATHESIS ⚡ ·
    PB-MODEL ⚡ · PB-NUNCIUS ⚡ · PB-OPTIMIZER ⚡ · PB-ORDINATA ⚡ · PB-PRESSURA ⚡ · PB-PROCESSUS ⚡ ·
    PB-SOLUM ⚡ (solum.fab + solum/path) · PB-TEMPUS ⚡ · PB-TENSOR ⚡ · PB-THESAURUS ⚡ · PB-TOML ⚡ ·
    PB-VECTOR ⚡ · PB-YAML ⚡
F4  PB-CONSUMERS                        (exempla corpus + package exempla + remaining proba references)
F5  PB-PACKS                            (radix-side filed gap: en/la pack [[library_members]] currency)
F6  PB-DOCS                             (module headers, AGENTS/README, scripta/check-source guards)
```

27 units. All src units are ⚡ leaves except the F1 chain and F2; F1/F2 must land before F4. PB-PACKS must
land in the same merge wave as the first rename unit that touches a pack-rowed identity (PB-CHORDA).

> Count corrected 2026-08-16: the graph below is **29 units** (F1 3 + F2 1 + F3 22 + F4 1 + F6 1 = 28 norma
> units, plus F5 PB-PACKS radix-coordinated = 29). The draft's "27" was a miscount. RA-1 (delivery.md §4)
> is a recommended pre-Pass-B parity precondition for the 5 pre-existing-error modules, not a Pass B unit.

**Unit discipline (all src units).** Each unit renames **its own module's** identifiers per its ledger and
updates **every in-repo reference** to those identifiers — other `src/**/*.fab` files and its own proba —
plus the module's header stem list. Param names that are Latin (`clavis`, `via`, `mensura`, `arcana`,
`aperta`, `signatura`, `nomen`, …) follow the §5 shared-verb table; already-English params stay. It does
**not** touch other modules' identifiers, the exempla corpus (PB-CONSUMERS), or packs (PB-PACKS). This
keeps the norma package (`faber test .`) green after every unit.

## 7. Unit specs

Common fields per src unit unless overridden: `write_scope` = the module's `.fab` file(s) + every in-repo
`src/**/*.fab` + proba that references this unit's renamed identifiers + the module header; `forbidden_scope`
= exempla/ (PB-CONSUMERS), packs (PB-PACKS), other modules' identifier renames, `./scripta/check-source`
gates (lane-owned); `risk` = `low` for leaves with no in-src callers, `medium` where a ledger row touches a
probe (§5.8) or a wide callsite reach (chorda, valor, caelum); `integrable` = `yes` (package green alone;
exempla-red until PB-CONSUMERS — see §8 merge gate); `est_basis` per unit.

**Common done_when (each src unit):**
1. Every ledger row applied in the module files and all in-repo references; the module's ledger Latin names
   appear in `src/` only as comment text, never as code tokens.
2. `faber check src/<m>.fab` (and each proba) exits 0 (lane authority setup: `FABER_BIN` from the packet,
   `FABER_LIBRARY_HOME` = parent of the repo root).
3. `faber test` per file on the module's proba reports **zero semantic errors** (SEM006/SEM008/SEM010/…) — the norma root has no `faber.toml`, so the per-file oracle replaces the vacuous root `faber test .` (§4.6; delivery.md §7 Q4 default: no new manifest).
4. `./scripta/check-source` green for the touched surface.
5. `git diff --check` silent.

**Common sanity (narrow, not a lane gate):** `faber check` on the module pair; the ledger grep guard;
`git diff --check`.

---

### F1 — Foundation

#### PB-CHORDA — `src/chorda.fab` (L1 proof row; en pack has a total identity for this module)

| outcome | chorda module English surface complete: 18 Latin identifiers renamed; all in-repo references updated |
| --- | --- |
| ledger | `retorta`→`reverse`; `nexa`→`join`; `reputat`→`count`; `reciprocus`→`is_palindrome`; `scala`→`chars`; `retenta_iudicio`→`keep_if`; `retenta`→`keep_between`; `expurgata_iudicio`→`drop_if`; `expurgata`→`drop_between`; `residuum`→`suffix`; `_finis_delimitris`→`_delimiter_end`; `_amputa_postremam_newline`→`_trim_trailing_newlines`; `diducta`→`split_frontmatter`; `comprimata`→`collapse_spaces`; `angustata`→`to_ascii`; `temptat`→`try_to_ascii`; `discissa`→`split_n`; trio: `pange`→`encode`, `solve`→`decode`, `tempta`→`try_decode`. Keep: `_newline` |
| probe | `fn reverse` (matches en pack surface row — no collision); trio naming per §11 Q1 |
| risk | medium (imported by csv + valor; exempla corpus references it until F4) |
| est_basis | pilot; 18 renames + callsite sweep (csv.fab, valor.fab) |
| note | MUST land with PB-PACKS (en pack `norma:chorda` total identity) |

#### PB-CSV — `src/csv.fab`

| outcome | csv module English surface complete |
| --- | --- |
| ledger | `_normaliza_row`→`_normalize_row`; trio: `solve`→`parse`, `pange`→`stringify`. Keep: `chorda` import alias (coordinate) |
| risk | low-medium (chorda references updated by PB-CHORDA; csv is a leaf otherwise) |
| est_basis | pilot; 3 renames |

#### PB-VALOR — `src/valor.fab`

| outcome | valor module English surface complete |
| --- | --- |
| ledger | predicates: `nihilne`→`is_null`, `bivalensne`→`is_bool`, `textusne`→`is_string`, `numerusne`→`is_int`, `fractusne`→`is_float`, `listane`→`is_list`, `tabulane`→`is_map`, `instansne`→`is_instant`; `_cape`→`_get`; `cape`→`get`; `carpe`→`get_index`; `inveni`→`find`; `inveni_partem`→`find_part`; `exige`→`require_string` (`require` is an en keyword); `exige_claves`→`require_strings`. Keep: `chorda` import alias |
| risk | medium (tree-navigation surface used by toml/yaml exempla until F4) |
| est_basis | pilot; 15 renames |

### F2 — Caelum family

#### PB-CAELUM — `src/caelum.fab` + `src/caelum/terminus.fab` + `src/caelum/connexus.fab` + `src/caelum/auscultator.fab`

| outcome | caelum family English surface complete; internal type chain renamed in one logical change |
| --- | --- |
| ledger | caelum: `ausculta`→`listen`, `auscultabit`→`listen_async`, `connecte`→`connect`, `connectet`→`connect_async`. terminus: genus `Terminus`→`Endpoint`; fields `hospes`→`host`, `portus`→`port`. connexus: genus `Connexus`→`Socket`; `hauri`→`read_bytes`, `hauriet`→`read_bytes_async`, `funde`→`write_bytes`, `fundet`→`write_bytes_async`, `claude`→`close`, `remotus`→`remote`, `localis`→`local`. auscultator: genus `Auscultator`→`Listener`; `accipe`→`accept` (listener context), `accipiet`→`accept_async`, `claude`→`close`, `portus`→`port` |
| probe | `fn accept` clean; `fn connect` clean |
| risk | medium (module chain; proba files reference method names only in prose until F4) |
| est_basis | pilot; ~18 renames |

### F3 — Leaves (all ⚡ caller-disjoint; any batch order)

#### PB-ALEATOR — `src/aleator.fab`

| outcome | aleator module English surface complete |
| --- | --- |
| ledger | `fractum`→`random_float` (bare `float` is a type); `sortire`→`random_int`; `octetos`→`random_bytes`; `semina`→`seed`. Keep: `uuid` |
| risk | low |
| est_basis | pilot; 4 renames |

#### PB-ARCA — `src/arca.fab`

| outcome | arca module English surface complete |
| --- | --- |
| ledger | `connectet`→`connect`; implendum `Connexio`→`Connection`, `Transactio`→`Transaction`; methods: `quaeret`→`query`, `capiet`→`first_row`, `quaerent`→`query_stream`, `exsequetur`→`execute`, `inseret`→`insert`, `incipiet`→`begin`, `claude`→`close`, `aperta`→`is_open`, `committet`→`commit`, `revertet`→`rollback` |
| risk | low (no in-src callers; mori-stub bodies) |
| est_basis | pilot; ~15 renames |

#### PB-CODEX — `src/codex.fab`

| outcome | codex module English surface complete (mixedCase clean-break) |
| --- | --- |
| ledger | `deBase64`→`decode_base64`; `temptaBase64`→`try_decode_base64`; `deHex`→`decode_hex`; `temptaHex`→`try_decode_hex`; `deUrl`→`decode_url`; `urlComponentum`→`encode_url_component`; `deUrlComponentum`→`decode_url_component`. Keep: `base64`, `hex`, `url` (English loanwords — Grade D) |
| risk | low |
| est_basis | pilot; 7 renames |

#### PB-CONSOLUM — `src/consolum.fab`

| outcome | consolum module English surface complete |
| --- | --- |
| ledger | `hauri`→`read_bytes`, `hauriet`→`read_bytes_async`; `lege`→`read_line`, `leget`→`read_line_async`; `funde`→`write_bytes`, `fundet`→`write_bytes_async`; `scribe`→`write_line`, `scribet`→`write_line_async`; `dic`→`write_partial`, `dicet`→`write_partial_async`; `mone`→`warn_line`, `monet`→`warn_line_async`; `vide`→`debug_line`, `videbit`→`debug_line_async`; `audit`→`stdin_is_tty`, `loquitur`→`stdout_is_tty`, `admonet`→`stderr_is_tty` |
| risk | low-medium (examples/coreutils + vivilite call `dic`/`mone` — la-pack rows preserve them) |
| est_basis | pilot; 17 renames |

#### PB-CRYPTA — `src/crypta.fab`

| outcome | crypta module English surface complete |
| --- | --- |
| ledger | `digere`→`digest`; `authenfica`→`hmac`; `cela`→`encrypt`; `revela`→`decrypt`; `signa`→`sign`; `verifica`→`verify`; `genera`→`generate_key`; `gemina`→`generate_pair`; `derivabit`→`derive_key`; implendum `ParClavium`→`KeyPair`, methods `publica`→`public_key`, `privata`→`private_key` (`public`/`private` are en keywords), `algorithmus`→`algorithm`; privata helpers: `_dextra32/64`→`_rotate_right32/64`, `_verbum32/64`→`_word32/64`, `_schema32/64`→`_schedule32/64`, `_comprima32/64`→`_compress32/64`, `_seria32/64`→`_serialize32/64`, `_padda32/64`→`_pad32/64`. Keep: `_sha256`, `_sha384`, `_sha512`, `_sha512_engine`, `_K32`, `_K64`, `_sigma0_*`, `_Sigma0_*` (standard crypto names) |
| risk | low-medium (exempla/crypta-sha2 references `crypta.digere` until F4) |
| est_basis | pilot-medium; ~28 renames |

#### PB-FILA — `src/fila.fab`

| outcome | fila module English surface complete |
| --- | --- |
| ledger | genus `Fila`→`Deque`; fields `caput`→`head`, `post`→`tail`; `longitudo`→`length`, `vacua`→`is_empty`, `appende`→`append`, `praepone`→`prepend`, `detrahe`→`pop_back`, `decapita`→`pop_front`, `primus`→`first`, `ultimus`→`last`, `collecta`→`to_list`, `creata`→`empty`, `creata_ex`→`from_list` — constructors keep their explicit type param (`empty<T>()`, `from_list<T>(list<T>)`); call sites construct via turbofish (`fila.empty<T>()`) |
| risk | low (no in-src callers; target-form module) |
| est_basis | pilot; ~15 renames |

#### PB-HTTP — `src/http.fab`

| outcome | http module English surface complete |
| --- | --- |
| ledger | client: `petet`→`get`, `mittet`→`post`, `ponet`→`put`, `delet`→`delete`, `mutabit`→`patch`, `rogabit`→`send_request` (`request` collides with the builtin frame-type variant, SEM005 — see §5 rule 1); implendum `Replicatio`→`Response`: `corpus`→`body`, `corpus_octeti`→`body_bytes`, `corpus_json`→`body_json`, `capita`→`headers`, `caput`→`header`, `bene`→`is_ok`; implendum `Rogatio`→`Request`: `modus`→`method`, `via`→`path`, `corpus`→`body`, `corpus_json`→`body_json`, `capita`→`headers`, `caput`→`header`. Keep: `listen`, `accept`, `respond`, `stop`, `status`, `param` |
| risk | low |
| est_basis | pilot; ~16 renames |

#### PB-JSON — `src/json.fab` + `src/json/pange.fab` + `src/json/solve.fab` + `src/json/lexica.fab` + `src/json/cursor.fab`

| outcome | json wire floor family English surface complete (one logical change) |
| --- | --- |
| ledger | json/pange/solve: `pange`→`stringify`, `solve`→`parse`, `tempta`→`try_parse`; lexica privata: `_est_sponte`→`_is_whitespace`, `_salta_sponte`→`_skip_whitespace`, `_est_digita`→`_is_digit`, `_est_finitura`→`_is_terminator`, `_transeant_liber`→`_escape_char`, `_postulat_transeum`→`_needs_escape`, `_destruat_liber`→`_unescape_char`, `_valor_hexadecimalis`→`_hex_value`; cursor: genus `Resultus`→`ScanResult`, fields `v`→`payload` (bare `value` is an en type), `i`→`index` |
| risk | low-medium (json surface used by vivilite/ai-workbench/radix corpus until la-pack rows land) |
| est_basis | pilot; ~16 renames |

#### PB-MATHESIS — `src/mathesis.fab` + `src/mathesis.proba`

| outcome | mathesis module English surface complete; proba references updated |
| --- | --- |
| ledger | `constringens`→`clamp`; `addita`→`add`, `subtracta`→`subtract`, `multiplicata`→`multiply`, `divisa`→`divide`, `modulata`→`modulo`, `negativa`→`negate`; `minor`→`less`, `maior`→`greater`, `minime`→`at_most`, `maxime`→`at_least`; `coniuncta`→`bit_and`, `disiuncta`→`bit_or`, `alternata`→`bit_xor`, `complementata`→`bit_not`; `sinistrata`→`shift_left`, `dextrata`→`shift_right`; `sinus`→`sin`, `cosinus`→`cos`, `tangens`→`tan`, `exponens`→`exp`, `logarithmus`→`ln`, `logarithmus10`→`log10`, `radix`→`sqrt`, `potentia`→`pow`; privata: `_reduce_angle` keep, `_sinus_series`→`_sin_series`, `_cosinus_series`→`_cos_series`, `_exponens_series`→`_exp_series`, `_logarithmus_series`→`_ln_series`, `_radix_newton`→`_sqrt_newton`. Keep: `PI`, `E`, `TAU` |
| risk | low-medium (proba + exempla corpus reference `mathesis.addita` etc. until F4) |
| est_basis | pilot; ~31 renames |

#### PB-MODEL — `src/model.fab`

| outcome | model module English surface complete |
| --- | --- |
| ledger | genus `Diagnosticum`→`Diagnostic`, fields `severitas`→`severity`, `nuntius`→`message`; `formatum`→`detect_format`; `diagnosticum`→`diagnose`; `severitas_pro_status`→`severity_for_status`; `inanis_diagnosticum`→`empty_diagnostic` |
| risk | low (ai-workbench already references nonexistent `model.*` members — pre-existing breakage, named finding) |
| est_basis | pilot; 7 renames |

#### PB-NUNCIUS — `src/nuncius.fab`

| outcome | nuncius module English surface complete |
| --- | --- |
| ledger | `alloca`→`allocate`; `aperi`→`open_pair`; `restringe`→`create_mutex`; `numera`→`create_semaphore`; `vigila`→`create_condvar`; implendum `ParPortarum`→`PortPair` (`a`→`first`, `b`→`second`); `Porta`→`Port` (`mitte`→`send`, `recipiet`→`receive`, `paratum`→`ready`, `claude`→`close`); `Mutex` keep (`obstringe`→`lock`, `tempta`→`try_lock`, `solve`→`unlock`); `Semaphorum`→`Semaphore` (`exspecta`→`wait`, `tempta`→`try_wait`, `signa`→`post`, `lege`→`read_value`); `Conditio`→`Condition` (`exspecta`→`wait`, `morare`→`wait_timeout`, `signa`→`signal`, `diffunde`→`broadcast`) |
| risk | low |
| est_basis | pilot-medium; ~25 renames |

#### PB-OPTIMIZER — `src/optimizer.fab`

| outcome | optimizer module English surface complete (already English — verify only) |
| --- | --- |
| ledger | Keep: `sgd_step`, `param`, `grad`, `lr` (English) |
| risk | low |
| est_basis | pilot; 0 renames (verification-only unit) |

#### PB-ORDINATA — `src/ordinata.fab`

| outcome | ordinata module English surface complete |
| --- | --- |
| ledger | genus `Ordinata`→`SortedMap`; `longitudo`→`length`, `vacua`→`is_empty`, `accipe`→`get`, `habet`→`has`, `pone`→`put`, `dele`→`delete`, `claves`→`keys`, `valores`→`values`, `creata`→`empty`, `creata_ex`→`from_map`, `_insere`→`_insert`, `_amputa`→`_remove`, `_amputa_ad`→`_remove_at`, `_aequa`→`_equals` (RA-1-added privata helper). Keep: fields `index`, `entries` (English). **Struck (RA-1 d3d5931, no target):** `fines`→`keys_between` and `_insere_ad`→`_insert_at` — both members removed (generic `<` ordering unavailable); `_insere` keeps the index sorted via `sort()`. Constructors declare their own type params (`creata<K, V>()`, `creata_ex<K, V>(map<K, V>)`) with type-args-inferred `Ordinata { }`; call sites construct via turbofish (`ordinata.empty<K, V>()`) |
| risk | low |
| est_basis | pilot; ~15 renames |

#### PB-PRESSURA — `src/pressura.fab`

| outcome | pressura module English surface complete |
| --- | --- |
| ledger | `comprime`→`compress`, `solve`→`decompress`, `comprimens`→`create_compressor`, `solvens`→`create_decompressor`; `Compressor`/`Decompressor` keep; methods `funde`→`write_bytes`, `hauri`→`read_bytes`, `claude`→`finish` |
| risk | low |
| est_basis | pilot; 9 renames |

#### PB-PROCESSUS — `src/processus.fab`

| outcome | processus module English surface complete |
| --- | --- |
| ledger | genus `Subprocessus`→`Subprocess`; `genera`→`spawn`, `dimitte`→`detach`, `captura`→`capture`, `exsequi`→`exec`, `exsequetur`→`exec_async`, `lege`→`read_env`, `scribe`→`write_env`, `sedes`→`cwd`, `muta`→`chdir`, `identitas`→`pid`, `argumenta`→`argv` (`args` is an en keyword), `exi`→`exit_process` (`exit` is an en keyword). Keep: `pid`, `args` params |
| risk | low-medium (examples/coreutils call `processus.sedes`/`lege` — la-pack rows preserve) |
| est_basis | pilot; 13 renames |

#### PB-SOLUM — `src/solum.fab` + `src/solum/path.fab`

| outcome | solum file + path module English surface complete (path proxies share identifiers — one unit) |
| --- | --- |
| ledger | file: `lege`→`read_file`, `leget`→`read_file_async`, `hauri`→`read_bytes`, `hauriet`→`read_bytes_async`, `partem`→`read_range`, `inveni`→`find`, `carpe`→`read_lines`, `carpiet`→`read_lines_async`, `scribe`→`write_file`, `scribet`→`write_file_async`, `funde`→`write_bytes`, `fundet`→`write_bytes_async`, `appone`→`append`, `apponet`→`append_async`, `exstat`→`exists`, `exstabit`→`exists_async`, `directoriumne`→`is_directory`, `regularene`→`is_regular`, `legibilene`→`is_readable`, `vinculumne`→`is_symlink`, `mensura`→`byte_length`, `modus`→`mode`, `describe`→`stat`, `describet`→`stat_async`, `sequere`→`read_link`, `sequetur`→`read_link_async`, `vincula`→`create_link`, `dele`→`delete`, `delet`→`delete_async`, `exscribe`→`copy`, `exscribet`→`copy_async`, `renomina`→`rename`, `renominabit`→`rename_async`, `tange`→`touch`, `tanget`→`touch_async`, `modum`→`set_mode`, `crea`→`create_dir`, `creabit`→`create_dir_async`, `enumera`→`list_dir`, `enumerabit`→`list_dir_async`, `amputa`→`remove_tree`, `amputabit`→`remove_tree_async`, `iunge`→`join`, `parens`→`parent`, `nomen`→`file_name`, `suffixum`→`extension`, `absolve`→`resolve`, `domus`→`home`, `temporarium`→`temp_dir`, `_iunge_duo`→`_join_two`, `explora`→`glob`; genus `SolumStatus`→`FileStatus`, fields `modus`→`mode`, `nexus`→`link_count`, `possessor`→`owner`, `grex`→`group`, `mensura`→`byte_length`, `modificatum`→`modified`, `directoriumne`→`is_directory`, `vinculumne`→`is_symlink`. path: `iunge`→`join`, `parens`→`parent`, `nomen`→`file_name`, `suffixum`→`extension`, `absolve`→`resolve`, `domus`→`home`, `temporarium`→`temp_dir` |
| risk | medium (widest callsite reach: examples/coreutils, vivilite, ai-workbench, radix corpus, faberlang.dev call these members — la-pack rows preserve la consumers) |
| est_basis | pilot-medium; ~55 renames |

#### PB-TEMPUS — `src/tempus.fab`

| outcome | tempus module English surface complete |
| --- | --- |
| ledger | `_duo`→`_pad2`, `_quattuor`→`_pad4`, `_campus`→`_field`, `_wire` keep; `solve`→`parse`, `exprimit`→`render` (`format` is an en keyword); `annus`→`year`, `mensis`→`month`, `dies`→`day`, `hora`→`hour`, `minuta`→`minute`, `secunda`→`second`; `structa`→`construct`, `structa_momenti`→`construct_moment`; `nunc`→`now`, `monotonicum`→`monotonic`, `activum`→`active`; `dormiet`→`sleep_async`, `expectet`→`sleep`, `vigila`→`interval`; `MILLISECUNDUM`→`MILLISECOND`, `SECUNDUM`→`SECOND`, `MINUTUM`→`MINUTE`, `HORA`→`HOUR`, `DIES`→`DAY` |
| risk | low-medium (radix corpus + exempla reference `tempus.*` until F4/la-pack rows) |
| est_basis | pilot; 25 renames |

#### PB-TENSOR — `src/tensor.fab`

| outcome | tensor module English surface complete |
| --- | --- |
| ledger | `planata`→`flatten`, `structa`→`construct`, `productum`→`product`, `applicata`→`apply`, `summa`→`sum`, `summa_fractus`→`sum_f32` |
| risk | low-medium (hosts/llvm exemplum + exempla corpus reference `tensor.structa/applicata/planata` until F4) |
| est_basis | pilot; 6 renames |

#### PB-THESAURUS — `src/thesaurus.fab`

| outcome | thesaurus module English surface complete |
| --- | --- |
| ledger | `capiet`→`get`, `ponet`→`put`, `condet`→`put_if_absent`, `delet`→`delete`, `exstabit`→`exists`, `expirabit`→`expire`, `augebit`→`increment`, `minuet`→`decrement`, `quaeret`→`find_keys`, `publicabit`→`publish`, `auscultabit`→`subscribe`; implendum `Subscriptio`→`Subscription` (`nuntiabunt`→`messages`, `claude`→`close`); `Nuntius`→`Message` (`thema`→`topic`, `corpus`→`body`, `tempus`→`timestamp`). Keep: `ttl` |
| risk | low |
| est_basis | pilot; ~16 renames |

#### PB-TOML — `src/toml.fab`

| outcome | toml module English surface complete |
| --- | --- |
| ledger | trio: `pange`→`stringify`, `solve`→`parse`, `tempta`→`try_parse` |
| risk | low (radix corpus instans.fab uses `toml.solve` — la-pack rows preserve) |
| est_basis | pilot; 3 renames |

#### PB-VECTOR — `src/vector.fab`

| outcome | vector module English surface complete |
| --- | --- |
| ledger | `addita`→`add`, `subtrahe`→`subtract`, `multiplica`→`multiply`, `divida`→`divide`, `productum`→`dot`, `transversum`→`cross`. ~~Keep: `swizzle`~~ — `swizzle` removed (RA-1 d3d5931): the generic wrapper's result width = swizzle pattern length, unresolvable for a size-N receiver (SEM014); the intrinsic stays compiler-exposed until the shape-param gap closes |
| risk | low |
| est_basis | pilot; 6 renames |

#### PB-YAML — `src/yaml.fab`

| outcome | yaml module English surface complete |
| --- | --- |
| ledger | `pange`→`stringify`, `necto`→`stringify_all`, `solve`→`parse`, `tempta`→`try_parse`, `collige`→`parse_all` |
| risk | low |
| est_basis | pilot; 5 renames |

### F4 — Consumers

#### PB-CONSUMERS — exempla corpus + package exempla

| outcome | all in-repo consumers track the new English surface; exempla corpus + packages green again |
| --- | --- |
| write_scope | `exempla/stdlib-nativum/*.fab` (17), `exempla/chorda/*.fab` (5), `exempla/caelum/*.proba` (3), `exempla/crypta-sha2/**`, `exempla/ad-multiplica-backward/**`, exempla `README.md` only where it quotes code |
| ledger | 1. Rename every reference to the renamed norma identifiers across all exempla (the full §7 ledger applied to call sites — e.g. `chorda.retorta`→`chorda.reverse`, `chorda.pange`→`chorda.encode`, `mathesis.addita`→`mathesis.add`, `tempus.structa`→`tempus.construct`, `solum.explora`→`solum.glob`, `crypta.digere`→`crypta.digest`, `valor.cape`→`valor.get`, `tensor.structa`→`tensor.construct`, `csv.solve`→`csv.parse`, `aleator.fractum`→`aleator.random_float`; fila/ordinata constructor call sites construct via turbofish — `fila.empty<T>()`, `fila.from_list<T>(…)`, `ordinata.empty<K, V>()`, `ordinata.from_map<K, V>(…)` — per the RA-1 constructor shape (none currently in the exempla corpus, verified 2026-08-16)). 2. Exemplum-local Latin identifiers follow the same conventions (e.g. tempus-civil `epochZero`/`build`/`buildMoment` keep — already English). 3. Frontmatter `syntax =`/`summary` fields updated to the new member names where they quote them |
| done_when | no ledger Latin name remains as a code token in `exempla/` (comment mentions allowed); `faber check` exit 0 on each exempla corpus file + each package exemplum (lane authority setup); `faber test .` still zero semantic errors; `./scripta/check-source` green |
| non_goals | external repos (examples/, radix/corpus, faberlang.dev, hosts — §4.5 findings); prose comment language refresh |
| risk | medium (wide but fully enumerated surface) |
| integrable | yes (closes the exempla-red window) |
| est_basis | medium; ~120 call-site renames + local names |

### F5 — Pack currency (radix-side filed gap; not a norma Hand)

#### PB-PACKS — en/la `[[library_members]]` row currency

| outcome | en pack `norma:chorda` rows track new English canonicals; la pack gains rows so la readers keep the Latin surface |
| --- | --- |
| scope | `radix/stdlib/locale/en/pack.toml` + `la/pack.toml` — filed back to radix (radix read-only for norma Hands); lands in the same merge wave as PB-CHORDA |
| ledger | en: rewrite `norma:chorda` rows to new canonicals (e.g. `reverse→reverse`, `join→join`, `encode→encode`, `decode→decode`, identity rows; drop stale `retorta`-era rows). la: add rows for every renamed public member (e.g. `reverse→retorta`, `join→nexa`, `read_file→lege`, `get→cape`, …) so la readers resolve the old Latin spellings |
| done_when | `cargo test -p radix locale` green (substring filter — `locale_*` is not a glob cargo honors, so it matched no tests and vacuous-passed; the substring hits the locale-pack and locale round-trip tests); a la-locale consumer compiling `chorda.retorta(...)`/`solum.lege(...)` resolves |
| integrable | yes (radix-side; gated with the first rename wave) |

### F6 — Docs / scripta

#### PB-DOCS — module headers, AGENTS/README, check-source guards

| outcome | live docs and source gates track the English surface |
| --- | --- |
| write_scope | `scripta/check-source` (add Pass B Latin-identifier guards: reserved escapes + the §7 verb rows), module headers in `src/**` (stem lists), `README.md`/`AGENTS.md` only where they quote member names |
| done_when | `./scripta/check-source` green; `rg` guard for the ledger Latin names in `src/` code tokens clean |
| non_goals | `docs/factory/*` prose, archived docs, comment-language refresh |
| risk | low |
| integrable | yes |
| est_basis | pilot |

## 8. Integration / merge gate

- Every Pass B unit is `integrable: yes` for the **norma package** (per-module `faber check` zero errors
  after each landing — RA-1 precondition for the 5 parity modules; `./scripta/check-source` green post-PA-4).
- **Exempla are red from the first src unit until PB-CONSUMERS lands** (they reference the old member names).
  Merge lane: **hold Pass B on `factory/merge` and land the family batch together with PB-CONSUMERS**
  (atomic landing), or land incrementally with an acknowledged exempla-red window.
- **PB-PACKS must land in the same wave as PB-CHORDA** (en pack `norma:chorda` total identity — see §4).
  This is a radix-side filed gap; the merge lane coordinates both repos.
- Family branches land on `factory/<lane>`; commit style: `refactor(norma): rename <module> identifiers to
  English (Pass B)` — one commit per unit.
- No aggregate Gate beyond the lane-owned validation (§9); F6 closes the goal gates.

## 9. Lane-owned validation (named once, not on children)

- **Lint lane**: stages 1–2 on the integrated tree (mechanical renames; `git diff --check`).
- **Test lane**: per-module `faber check` (default en pack, §4.6 toolchain contract), `faber test` on proba,
  `./scripta/check-source` (post-PA-4), exempla corpus + package exempla after F4.
- **Merge lane**: integrates `factory/<lane>` branches in §6 order; batches PB-CONSUMERS with the final
  family dispatch; coordinates PB-PACKS (radix) with PB-CHORDA; runs
  `scripta/verify-main-consistent` per repo.
- Pass A's own gates (converted en surface, manifest flips, pack completeness/round-trip proof) are
  preconditions, owned by the PA-5 successor (delivery.md §4).

## 10. Open questions for Mind

1. **Mechanical trio English** (GOAL "seed conventions from gradus"): lock `pange→encode/stringify`,
   `solve→decode/parse`, `tempta→try_decode/try_parse` per module domain (chorda=bytes; json/toml/yaml/csv/
   tempus=text↔value). Alternatives: keep `pange/solve/tempta` as-is (they are already short and would not
   need pack-row churn) — but the goal explicitly renames identifiers, so default is English.
2. **Async-twin suffix** (new convention, no gradus precedent): sync verb plain, `@ futura` twin + `_async`.
   Alternatives: `_future` (matches `@ future` annotation) or per-pair distinct verbs. Default `_async`.
3. **`nomen` escape**: locked `file_name` for path basenames (avoids the `name` annotation-key probe).
   Alternative: `base_name`. Confirm.
4. **Pack currency split**: PB-PACKS is a radix-side filed gap that must land with PB-CHORDA. Confirm the
   merge lane owns both repos; if radix Hands are blocked, PB-CHORDA holds until the pack row swap lands.
5. **External-caller follow-up** (§4.5): en-locale consumers (radix/corpus, faberlang.dev, hosts) break at
   member level after renames; la-locale consumers are preserved by PB-PACKS. Name an owner for the en
   follow-up (S3-style, like gradus's sibling-consumer follow-up).
6. **`model.fab` pre-existing breakage**: `examples/ai-workbench` calls `model.tempta_safetensors_summarium`,
   `model.tempta_gguf_summarium`, `model.gguf_quantization_fragmentum` which do **not** exist in the current
   module. Report to Mind — unrelated to Pass B but blocks ai-workbench regardless.
7. **Packet staleness**: the planner-2 packet has no norma member; the delivery spec is cited from the
   norma main checkout. Refresh a hand lane packet (norma member) before dispatch so Hands measure the
   post-A baseline this delivery assumes.
8. **Batch dispatch of the ⚡ leaves** (F3) may join any wave in any order; PB-OPTIMIZER is
   verification-only and may batch with any neighbor.
9. **RA-1 parity precondition** (delivery.md §4): land the 5 pre-existing-error repairs (mathesis, fila,
   ordinata, vector, json/pange) before PB-MATHESIS/PB-FILA/PB-ORDINATA/PB-VECTOR/PB-JSON, or amend those
   units' done_when to "no new errors; parity unchanged". Default: land first.
10. **SEM006 visibility follow-up** (delivery.md §4, excluded from this delivery per task non-goals):
   caelum family + exempla SEM004/SEM006 + crypta-sha2 private-member calls are pre-existing; the c006af5
   author filed them. Confirm ownership/timing with Mind.
11. **FABER_BIN currency** (§4.6): verify the lane packet's dev build postdates radix hand-6 `71b0f6801`
    before Hands measure; the stale binary silently misreports csv/valor and importing-module checks.

## 11. Honesty notes

- Per-module ledgers were derived from the live tree (function/type/field census per module, §4) plus the
  gradus seed and morphologia meanings. Every Latin identifier in `src/` is named in a ledger row above.
  Re-verified 2026-08-16 against live `norma/src` (census + spot-checks; all ledger names present).
- `_`-prefixed privata helpers are included in the rename surface (clean break leaves no Latin identifiers
  in `src/`); they are exempt from any coverage gate (norma has no api-reference-style gate).
- The capsule-free import DAG means no merge-forcing cycles; the caelum chain and chorda→{csv,valor} edges
  are the only serialization constraints.
- Per-module `faber check` zero errors is the unit oracle; FMIR runtime-stepper executed-value residuals
  (if any appear in proba) are out of this wave — report, don't chase.
- "Zero-errors-per-landing" is measured on the **post-Pass-A en surface**; Pass A must land first
  (PA-1 integrate + PA-2 manifests + PA-3 proba + PA-4 scripta gates; PA-5 pack completeness is the A2
  precondition). The 5 parity modules and the SEM006 visibility class are pre-existing residuals named in
  delivery.md §3–§4, not Pass B regressions.
