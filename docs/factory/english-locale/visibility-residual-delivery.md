# Delivery — Norma SEM006 visibility residual (caelum + exempla + crypta-sha2)

**Planner**: planner. **Assignment**: task `03c9dd34` (Mind, 2026-08-17).
**Goal**: [`goal.md`](goal.md) (`gol_ecc7b670a90073a2`) — **stays done**. This is residual
lowering, not a reopen of Pass A/B locale conversion.
**Registered want / need**: want `390d295c` / need `794c7466`.
**Predecessor**: CTO `662fdf89` finding 5 — `correct_before_next_phase` on visibility
before any C1 implementation Hand.
**Repo**: norma (direct on `main` @ `54b9857`); radix **read-only except** the named
chorda pack unit.
**Related (do not execute)**: [`test-coverage/delivery.md`](../test-coverage/delivery.md)
`NORMA-VIS-EXPOSE` — this spec is the `390d295c` slice of that precondition.

**Status**: **READY to dispatch.** Nine Hand units. Goal-check of the parent goal is
not reopened.

---

## 1. Interpreted Unit

Pass A/B converted Norma to an English reader surface and renamed members. The
visibility model (radix VM-U2/U4) is: unmarked / `@ private` = module-private;
`@ public` is required to import or name a member across a file. Several
first-party surfaces still teach or return unmarked members, so `faber check`
reports `SEM006.import_module_private` and `SEM004.namespace_missing_export`.
Cascade `SEM010` follows the failed name.

This delivery exposes the **intended public surface already taught by first-party
exempla** (and the caelum type chain those errors sit on). It does not convert
locale, rename members, author C1 proba, or refresh Latin comments.

**Admitted scope (completion contract):**

| Named residual | Live measurement (norma `54b9857`, rebuilt `faber` 1.7.0, `FABER_LIBRARY_HOME` = parent of norma) |
| --- | --- |
| `src/caelum.fab` 1×SEM006 | cascaded from imported `auscultator.accept() → connexus.Socket` |
| `src/caelum/auscultator.fab` 2×SEM006 | `accept` / `accept_async` return unmarked `connexus.Socket` |
| exempla SEM004/SEM006 private-member | chorda (5 files, SEM004), `codex.fab` (SEM006×10), `mathesis.fab` + `mathesis-operators.fab` (SEM006), `tempus-civil.fab` (SEM006×21), `tensor-applicata.fab` / `tensor-bridge.fab` (SEM006 + one SEM004 on missing `creata`), `toml-navigatio.fab` (SEM006×5 on `norma:valor`) |
| crypta-sha2 SEM006/SEM010 | package check: SEM006×16 + SEM010×33, all on `crypta.digest` |

**Explicitly not this wave (measured, different class):**

| Surface | Why excluded |
| --- | --- |
| Pass A/B identifier/locale work | task `do_not`; conversion has landed |
| C1+ proba authoring | task `do_not`; coverage stays in `test-coverage/delivery.md` |
| Remaining Latin comments / `# Verb:` headers | want `db928be6` |
| `exempla/stdlib-nativum/vector-pending-placeholder.fab` SEM004×2 | intentional expected-failure (`vector.create` / `vector.shuffle` do not exist) |
| `tensor.creata<…>(…)` SEM004 | no `fn creata`/`fn create` in `src/tensor.fab`; comment says compiler-owned spelling — missing name, not a private member |
| `exempla/stdlib-nativum/solum.fab` SEM001/SEM008 | `import from "norma:solum" only` then `only.glob` — identifier/import shape, not SEM004/SEM006 |
| Full 34-module C1 visibility catalog | this slice is `390d295c`; http stubs, unmarked `solum.glob`/`stat`, crypta Stage-2 stubs, tempus `interval`/unit constants stay unless a later catalog unit admits them |

---

## 2. Normalized Spec

After this wave:

1. Every **live, non-helper** member that a first-party exemplum in the admitted
   set already calls is `@ public` on its defining Norma module.
2. The caelum family public surface (`Endpoint` already is) is annotated:
   `Socket`, `Listener`, their ledger methods, and `listen`/`connect` (+ `_async`).
3. `crypta.digest` is `@ public`. SHA-2 helpers stay `@ private`. Stage-2 stubs
   stay unmarked.
4. en-pack `norma:chorda` remains a **total identity**: every newly public chorda
   member has an identity row. la pack gains the matching Latin surface rows from
   the PB-CHORDA ledger. No other pack census.
5. Cascade SEM010/SEM011 on the named consumer files clear because the callee
   type exists again. Independent leftovers (`tensor.creata` SEM004, toml SEM016
   if it survives) are reported, not invented away.
6. No Pass A/B reopen. No C1 `.proba` files. No comment-language refresh.

### Architecture lock (do not re-decide in a Hand)

Visibility model (radix `import_seam.rs`): unmarked = module-private; `@ public`
required across a file. Same-package `@ interna` is unused here (norma src is
not one `faber.toml` package with the exempla).

**Promote, do not shrink.** First-party exempla that call a live member define
that member as intended-public. Do not rewrite those exempla down to the current
9-row chorda pack just to avoid annotations.

**Catalog rule:**

| Module shape | What to mark `@ public` |
| --- | --- |
| Already has a deliberate `@ public` subset (chorda, tempus, valor) | Residual first-party-called family in that module, plus documented twins in the same section (`to_ascii` with `try_to_ascii`) |
| No `@ public` split; helpers are `_` / `@ private` (mathesis, codex, tensor) | The existing non-helper catalog. Do not add functions |
| crypta | **Exception (task scope):** `digest` only. Stubs are Stage 2; helpers already `@ private` |
| caelum family | Public types + facade + ledger methods. `terminus.Endpoint` is already `@ public` |

**Never** export `_` helpers or `@ private` engines. **Never** invent
`vector.create`, `vector.shuffle`, or `tensor.creata`.

CTO `662fdf89` C1 hard_gate: every member a first-party exemplum or colocated
proba calls is `@ public` (or the test is rewritten). This wave takes the
promote side for live members. `VIS-MATHESIS` therefore also clears
`src/mathesis.proba` SEM006 on `PI`/`add` as a **side effect**. That is not a
C1 coverage unit.

### Non-goals

- Reopen or amend Pass A/B ledgers, param policy, or pack “completeness” prose
- Author or enlarge `.proba` coverage (C1+)
- Convert comments, `# Verb:` / `# Name:` headers, or exempla filenames
- Export crypta stubs (`hmac`, `encrypt`, …) or `KeyPair`
- Add la rows beyond the locked chorda census
- Fix solum `only` import, vector pending-placeholder, or `tensor.creata`
- Weakening diagnostics or skipping SEM006
- `./scripta/check-source`, `faber check` of the whole tree, `--stage` / `--e2e` / `--full` on a child Hand

---

## 3. Repo-Aware Baseline

**Toolchain (every sanity command):**

```text
FABER=/Users/ianzepp/work/faberlang/radix/target/debug/faber   # 1.7.0, rebuilt
FABER_LIBRARY_HOME=/Users/ianzepp/work/faberlang
cd /Users/ianzepp/work/faberlang/norma
```

A `/tmp` worktree without `FABER_LIBRARY_HOME` silently reads the main checkout
for `norma:*` imports (Pass A measurement note). Use the tree under test.

**Why the errors exist (code, not docs):**

| Defining file | Public today | Residual members the admitted consumers need |
| --- | --- | --- |
| `src/caelum/terminus.fab` | `@ public class Endpoint` | none |
| `src/caelum/connexus.fab` | none | `class Socket` (type used by auscultator); methods on the PB-CAELUM ledger |
| `src/caelum/auscultator.fab` | none | `class Listener`; `accept`/`accept_async`/`close`/`port` |
| `src/caelum.fab` | none | `listen`/`listen_async`/`connect`/`connect_async` |
| `src/crypta.fab` | none (`_sha*` are `@ private`) | `digest` only |
| `src/chorda.fab` | 9 fns (reverse/join/keep_if/keep_between/drop_between/split_frontmatter/split_n/encode/decode) | `count`, `is_palindrome`, `chars`, `drop_if`, `suffix`, `collapse_spaces`, `to_ascii`, `try_to_ascii`, `try_decode` |
| `src/codex.fab` | none | all 10 catalog fns (base64/hex/url family) |
| `src/mathesis.fab` | none | all non-`_` catalog fns (helpers `_reduce_angle`, `_sin_series`, … stay private) |
| `src/tempus.fab` | `now`/`monotonic`/`active`/`sleep`/`sleep_async`/`MILLISECOND` | civil family: `parse`/`render`/`construct`/`construct_moment`/`year`/`month`/`day`/`hour`/`minute`/`second` |
| `src/tensor.fab` | none | `flatten`/`construct`/`product`/`apply`/`sum`/`sum_f32` — not `creata` |
| `src/valor.fab` | `get`/`require_string`/`require_strings` | predicates `is_null`/`is_bool`/`is_string`/`is_int`/`is_float`/`is_list`/`is_map`/`is_instant`; `get_index`/`find`/`find_part`. `_get` stays private |

**Chorda pack coupling (only this module):**

- en `stdlib/locale/en/pack.toml` comments that `norma:chorda` is a **total
  identity** (9 rows today). `strict_library_members` is on for non-`la` packs
  that define the identity (`radix/crates/radix/src/semantic/scope.rs` 414–416).
- Marking a new chorda member `@ public` without an en identity row **skips**
  the export (`strict_locale_pack_member_omitted`). Exempla stay SEM004.
- la pack has the same 9 chorda rows (`retorta`/`nexa`/…). la is the identity
  locale pack, so unmapped members would install under English names; still add
  the Latin surfaces so a Latin reader of these newly public members does not
  lose the PB-CHORDA spellings.
- No other Norma module is a total identity on en. Other units do not need pack
  rows.

**SEM004 vs SEM006 (same class, different site):**

- `SEM006.import_module_private` — imported name exists and is module-private
  (codex, mathesis, tempus, tensor, valor, crypta, caelum `Socket`).
- `SEM004.namespace_missing_export` — namespace import succeeded; member is not
  on the export surface (chorda residual, `tensor.creata`, vector placeholder).

**Already clean first-party files (do not touch):** `diducta.fab`, `aleator.fab`,
`caelum-terminus.fab`, `consolum.fab`, `csv-chorda.fab`, `retorta.fab`,
`solum-explora-contract.fab`, `toml-exige-claves.fab`,
`exempla/ad-multiplica-backward`, `exempla/caelum/*.proba`.

---

## 4. Stage Graph

```text
VIS-CAELUM          ──┐
VIS-CRYPTA-DIGEST   ──┤
VIS-CODEX           ──┤
VIS-MATHESIS        ──┼── VIS-GATE (merge / test lane)
VIS-TEMPUS          ──┤
VIS-TENSOR          ──┤
VIS-VALOR           ──┤
VIS-CHORDA ──────────── VIS-CHORDA-PACKS ──┘
```

Eight Norma units are write-disjoint and may run in parallel. The pack unit
reads the locked chorda list below; it depends on `VIS-CHORDA` so its sanity
can see the new `@ public` members.

### Common Hand contract

- **Sanity (Hand only):** `faber check` on the write_scope module + the named
  consumer file(s). `git diff --check` silent. Never
  `./scripta/check-source`, never `faber check` of all `src/`, never `--stage` /
  `--e2e` / `--full`.
- **Forbidden:** new functions; identifier renames; comment translation;
  `.proba` authoring; diagnostic suppression; exporting `_` / `@ private`.
- **integrable:** `yes` for every child. Dual-authority is not an issue
  (annotations and pack rows do not create two names for one member).
- **Oracle env:** toolchain block in §3.

---

### `VIS-CAELUM` — caelum family public types and facade

| Field | Value |
| --- | --- |
| `id` | `VIS-CAELUM` |
| `outcome` | Caelum family intended public surface is importable: `Socket`/`Listener` types, ledger methods, and `listen`/`connect` (+ `_async`). Clears the named src SEM006 pair. |
| `write_scope` | `src/caelum.fab`, `src/caelum/connexus.fab`, `src/caelum/auscultator.fab` |
| `read_scope` | `src/caelum/terminus.fab` (already `@ public`; do not edit), PB-CAELUM ledger in `pass-b-delivery.md` |
| `done_when` | `@ public` on `class Socket` and its seven methods; `@ public` on `class Listener` and its four methods; `@ public` on `listen`/`listen_async`/`connect`/`connect_async`. `faber check src/caelum.fab`, `src/caelum/auscultator.fab`, and `src/caelum/connexus.fab` report **zero SEM006**. `terminus.fab` still check-ok. |
| `depends_on` | none |
| `sanity` | the three `faber check` commands above |
| `non_goals` | Stage 2 bodies; `terminus.fab`; exempla/caelum proba (already clean); Latin comment refresh |
| `risk` | low — annotation-only type chain |
| `integrable` | yes |

---

### `VIS-CRYPTA-DIGEST` — public SHA-2 digest

| Field | Value |
| --- | --- |
| `id` | `VIS-CRYPTA-DIGEST` |
| `outcome` | `crypta.digest` is `@ public` so `exempla/crypta-sha2` can call the live SHA-2 surface. |
| `write_scope` | `src/crypta.fab` only (one annotation on `fn digest`) |
| `done_when` | `fn digest` has `@ public`. Helpers remain `@ private`. Stubs unmarked. `faber check exempla/crypta-sha2` has **zero SEM006**. Cascade SEM010 on that package is gone; if any SEM010 remains, report it — do not change digest's type or the NIST vectors. |
| `depends_on` | none |
| `sanity` | `faber check src/crypta.fab`; `faber check exempla/crypta-sha2` |
| `non_goals` | stub identity strings; `@ public` on `hmac`/`encrypt`/`decrypt`/`sign`/`verify`/`generate_key`/`generate_pair`/`derive_key`/`KeyPair`; new tests |
| `risk` | low |
| `integrable` | yes |

---

### `VIS-CHORDA` — remaining first-party chorda exports

| Field | Value |
| --- | --- |
| `id` | `VIS-CHORDA` |
| `outcome` | The residual chorda members first-party exempla already call (plus the `to_ascii` twin) are `@ public`. |
| `write_scope` | `src/chorda.fab` |
| `done_when` | `@ public` on exactly: `count`, `is_palindrome`, `chars`, `drop_if`, `suffix`, `collapse_spaces`, `to_ascii`, `try_to_ascii`, `try_decode`. Existing 9 stay public. `_newline` / `_delimiter_end` / `_trim_trailing_newlines` stay unmarked. `faber check src/chorda.fab` still exits 0. **Exempla SEM004 may remain until `VIS-CHORDA-PACKS`.** |
| `depends_on` | none |
| `sanity` | `faber check src/chorda.fab` |
| `non_goals` | pack.toml; rewriting exempla to the old 9-member set; comment headers |
| `risk` | medium — total-identity pack will skip these until the pack unit lands |
| `integrable` | yes |

---

### `VIS-CHORDA-PACKS` — en/la rows for the new chorda public set

| Field | Value |
| --- | --- |
| `id` | `VIS-CHORDA-PACKS` |
| `outcome` | en total identity and la Latin surfaces include the nine newly public chorda members. |
| `write_scope` | `radix/stdlib/locale/en/pack.toml`, `radix/stdlib/locale/la/pack.toml` — **only** new `package = "norma"` / `module_path = ["chorda"]` rows listed below |
| `done_when` | en has identity rows (`canonical` = `surface` = English name) for the nine. la has rows with English `canonical` and PB-CHORDA Latin `surface`: `count`←`reputat`, `is_palindrome`←`reciprocus`, `chars`←`scala`, `drop_if`←`expurgata_iudicio`, `suffix`←`residuum`, `collapse_spaces`←`comprimata`, `to_ascii`←`angustata`, `try_to_ascii`←`temptat`, `try_decode`←`tempta`. After `VIS-CHORDA`, these five files have **zero SEM004**: `exempla/chorda/angustat.fab`, `discidit.fab`, `mechanica.fab`, `retine.fab`, `exempla/stdlib-nativum/chorda.fab`. No other pack rows added. |
| `depends_on` | `VIS-CHORDA` |
| `sanity` | `faber check` on the five chorda exempla (env from §3) |
| `non_goals` | “complete” the 87-row la census; crypta/mathesis/caelum pack rows; policy.md rewrite |
| `risk` | medium — two-repo; wrong Latin surface would lie to la readers |
| `integrable` | yes |

---

### `VIS-CODEX` — public codec catalog

| Field | Value |
| --- | --- |
| `id` | `VIS-CODEX` |
| `outcome` | All ten `norma:codex` catalog functions are `@ public`. |
| `write_scope` | `src/codex.fab` |
| `done_when` | `@ public` on `base64`, `decode_base64`, `try_decode_base64`, `hex`, `decode_hex`, `try_decode_hex`, `url`, `decode_url`, `encode_url_component`, `decode_url_component`. `faber check exempla/stdlib-nativum/codex.fab` has **zero SEM006**. |
| `depends_on` | none |
| `sanity` | `faber check src/codex.fab`; `faber check exempla/stdlib-nativum/codex.fab` |
| `non_goals` | new codecs; comment refresh |
| `risk` | low |
| `integrable` | yes |

---

### `VIS-MATHESIS` — public math catalog

| Field | Value |
| --- | --- |
| `id` | `VIS-MATHESIS` |
| `outcome` | The non-helper mathesis catalog is `@ public`, clearing the two mathesis exempla (and, as a side effect, `src/mathesis.proba` SEM006 on `PI`/`add`). |
| `write_scope` | `src/mathesis.fab` |
| `done_when` | every non-`_` function in the file is `@ public`. `_reduce_angle`, `_sin_series`, `_cos_series`, `_exp_series`, `_ln_series`, `_sqrt_newton` stay unmarked. `faber check exempla/stdlib-nativum/mathesis.fab` and `mathesis-operators.fab` have **zero SEM006**. Cascade SEM010 on those two files is gone or reported if independent. **Do not edit any `.proba` file.** |
| `depends_on` | none |
| `sanity` | `faber check src/mathesis.fab`; the two exempla checks |
| `non_goals` | C1 cases; changing bodies; exporting helpers |
| `risk` | medium — expands the public catalog; unblocks C1 analysis of the existing proba without being a coverage Hand |
| `integrable` | yes |

---

### `VIS-TEMPUS` — public civil date/time API

| Field | Value |
| --- | --- |
| `id` | `VIS-TEMPUS` |
| `outcome` | The civil construct/parse/render/field family `tempus-civil.fab` already calls is `@ public`. |
| `write_scope` | `src/tempus.fab` |
| `done_when` | `@ public` on `parse`, `render`, `construct`, `construct_moment`, `year`, `month`, `day`, `hour`, `minute`, `second`. Existing clock/sleep/`MILLISECOND` markers unchanged. `_pad2`/`_pad4`/`_field`/`_wire` stay private. `interval`, `SECOND`, `MINUTE`, `HOUR`, `DAY` stay unmarked (not this residual). `faber check exempla/stdlib-nativum/tempus-civil.fab` has **zero SEM006**. Cascade SEM010/SEM011 on that file is gone or reported if independent. |
| `depends_on` | none |
| `sanity` | `faber check src/tempus.fab`; `faber check exempla/stdlib-nativum/tempus-civil.fab` |
| `non_goals` | remaining unmarked constants; comment headers |
| `risk` | low |
| `integrable` | yes |

---

### `VIS-TENSOR` — public tensor bridges

| Field | Value |
| --- | --- |
| `id` | `VIS-TENSOR` |
| `outcome` | Existing tensor functions the exempla call are `@ public`. |
| `write_scope` | `src/tensor.fab` |
| `done_when` | `@ public` on `flatten`, `construct`, `product`, `apply`, `sum`, `sum_f32`. No new `create`/`creata`. `faber check exempla/stdlib-nativum/tensor-applicata.fab` has **zero SEM006**. `tensor-bridge.fab` has **zero SEM006**. SEM004 on `tensor.creata` **may remain** (named leftover). |
| `depends_on` | none |
| `sanity` | `faber check src/tensor.fab`; the two tensor exempla |
| `non_goals` | inventing `creata`; rewriting the `creata` call site; compiler intrinsic work |
| `risk` | low |
| `integrable` | yes |

---

### `VIS-VALOR` — public predicates and find family

| Field | Value |
| --- | --- |
| `id` | `VIS-VALOR` |
| `outcome` | Valor predicates and find/get_index used by `toml-navigatio.fab` are `@ public`. |
| `write_scope` | `src/valor.fab` |
| `done_when` | `@ public` on `is_null`, `is_bool`, `is_string`, `is_int`, `is_float`, `is_list`, `is_map`, `is_instant`, `get_index`, `find`, `find_part`. `get` / `require_*` stay public. `_get` stays private. `faber check exempla/stdlib-nativum/toml-navigatio.fab` has **zero SEM006**. Remaining SEM016 (if any) is reported, not chased. |
| `depends_on` | none |
| `sanity` | `faber check src/valor.fab`; `faber check exempla/stdlib-nativum/toml-navigatio.fab` |
| `non_goals` | toml.parse/`try_parse` markers; changing find's type |
| `risk` | low |
| `integrable` | yes |

---

## 5. Implementation Work

Mind files Hands as a pointer: this path + unit `id`. Do not file C1 Hands
from this spec.

| id | write_scope | done_when (short) | depends_on | parallel |
| --- | --- | --- | --- | --- |
| `VIS-CAELUM` | `src/caelum.fab`, `src/caelum/connexus.fab`, `src/caelum/auscultator.fab` | zero SEM006 on those three checks | — | yes |
| `VIS-CRYPTA-DIGEST` | `src/crypta.fab` | `digest` public; crypta-sha2 zero SEM006 | — | yes |
| `VIS-CHORDA` | `src/chorda.fab` | nine members public; module check 0 | — | yes |
| `VIS-CHORDA-PACKS` | radix en+la `pack.toml` (chorda rows only) | five chorda exempla zero SEM004 | `VIS-CHORDA` | after chorda |
| `VIS-CODEX` | `src/codex.fab` | ten fns public; codex exemplum zero SEM006 | — | yes |
| `VIS-MATHESIS` | `src/mathesis.fab` | non-`_` catalog public; two exempla zero SEM006 | — | yes |
| `VIS-TEMPUS` | `src/tempus.fab` | civil family public; tempus-civil zero SEM006 | — | yes |
| `VIS-TENSOR` | `src/tensor.fab` | six fns public; tensor exempla zero SEM006 | — | yes |
| `VIS-VALOR` | `src/valor.fab` | predicates+find public; toml-navigatio zero SEM006 | — | yes |

---

## 6. Checkpoints And Gates

### Batching / Split Decision

**Several Hands + one merge gate.** One mega-Hand would bag eight modules and a
radix pack. Micro-units (one `@ public` line per Hand) would waste the seat.
Split is one defining module (or the caelum type family, or the chorda pack
follow-through).

`VIS-CHORDA` and `VIS-CHORDA-PACKS` must both land before anyone claims the
chorda exempla green. They may still land as two commits.

### Integration / merge gate (`VIS-GATE`)

Lane-owned, not a Hand.

After all nine children:

1. Re-run the §3 measurement on the admitted files.
2. Required green (zero SEM004/SEM006 of this class):

   - `src/caelum.fab`, `src/caelum/auscultator.fab`, `src/caelum/connexus.fab`
   - `exempla/crypta-sha2` (also zero cascade SEM010, or a named leftover)
   - the five chorda exempla listed in `VIS-CHORDA-PACKS`
   - `exempla/stdlib-nativum/{codex,mathesis,mathesis-operators,tempus-civil,tensor-applicata}.fab`
   - `tensor-bridge.fab` zero SEM006 (SEM004 `creata` allowed)
   - `toml-navigatio.fab` zero SEM006 (SEM016 allowed if still independent)

3. Required still-red / untouched: `vector-pending-placeholder.fab`; solum
   SEM001/SEM008; no new C1 proba files.

### Lane-owned validation (named once)

- **Lint:** `git diff --check` on landed paths. No format debate.
- **Test:** per-file `faber check` listed above. Optional: `faber test src/mathesis.proba`
  should now be **zero SEM006** (provider-runner failure is
  `OUT-RADIX-PROBA-IMPORTED-PROVIDER`, not a Norma Hand defect). Do not treat
  that runner failure as this wave's closeout.
- **Merge:** land Norma units on norma main; land `VIS-CHORDA-PACKS` on radix
  in the same visibility wave so chorda exempla can go green. No release tag
  (`not-applicable` — annotations + pack rows, no version/contract ceremony).

---

## 7. Validation

**Hand sanity:** see each unit. Narrow `faber check` only.

**Wave closeout (merge/test lane):** §6 `VIS-GATE`.

**Not validation for any child:** `./scripta/check-source`,
`./scripta/check-compile`, `faber test .`, package `--full`, C1 coverage counts.

---

## 8. Companion Skill Plan

| Skill | Use |
| --- | --- |
| `$delivery` / this spec | authority for Hand pointers |
| `$factory` | only if Mind runs factory; not required for annotation Hands |
| `$tugboat` | Mind files + spawns Hands; planner does not task Hands |
| `test-coverage/delivery.md` | C1 stays gated until this wave lands; do not execute it here |
| `$polish` | Hands polish the `.fab` / pack files they touch |

---

## 9. Open Questions

None blocking. Defaults below are in force.

1. **`tensor.creata` leftover** — compiler-owned Latin spelling vs missed
   English `create` vs rewrite the one exemplum line. Default: leave red;
   not visibility. Route later if C1 or a tensor goal admits it.
2. **crypta stubs public?** Default: no, until a first-party caller or Stage 2
   ships.
3. **tempus `interval` / `SECOND`…** Default: later catalog unit, not this
   residual.
4. **solum `only.glob` SEM001** — import-shape bug, not this class. Default:
   separate want if Mind wants it.
5. **C1 Hands after this wave** — Mind's call. `VIS-MATHESIS` removes the
   mathesis.proba SEM006 blocker; the provider-runner gap remains
   `OUT-RADIX-PROBA-IMPORTED-PROVIDER`.

---

## 10. Stop conditions

- A Hand starts renaming identifiers, converting locale, or authoring `.proba`.
- A Hand adds pack rows outside the locked nine chorda members.
- A Hand exports `_` helpers or invents `create`/`creata`/`shuffle`.
- `faber check` of a defining module is still SEM006 after the annotations
  (possible radix regression — route, do not weaken the exemplum).
- Scope creeps into compatibility-policy rewrite or Pass B param leftovers.
