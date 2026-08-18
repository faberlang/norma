+++
locale = "en"
+++

# Design ruling: finish the norma English rename — module names

**Status**: proposed — design ruling draft (head-cto, task f0b8ae1c; uncommitted, mind routes)

**Question**: norma's public members became English (Pass B, 2026-08-16) but the
module coordinates stayed Latin (`norma:solum`, `norma:chorda`, …) while
tela/gradus/triga present fully-English module surfaces. Two paths were tabled:
**(A)** rename the modules (mechanical, long-tail consumer sweep), or **(B)**
locale-aware module names (the pack maps module spellings at import
resolution; Latin stays canonical internally).

**Ruling**: **A — rename the modules.** B is rejected: the aliasing danger the
operator named is not containable, and B reopens a settled boundary without new
evidence. Details in §5–§6; unit list in §7.

Evidence is live code and docs at HEAD of the sibling checkouts (2026-08-18).
Counting rules are stated per table. Confidence tags: **known** (cited,
measured), **inferred** (reasoned from cited code, not executed),
**unverified** (stated by a doc/comment, not proven here).

---

## 1. Ruling

Rename every Latin module coordinate in `norma/src/` to English in one
pre-1.0 clean break. No alias map, no compatibility shim: after the rename the
old spelling fails import resolution, with a pointed diagnostic (the
retired-surface machinery already does exactly this for `norma:hal/*`).

Three facts decide it:

1. **The workspace convention is already "Latin package brand, English module
   surface."** gradus, tela, and triga all keep Latin package names with
   English module files (§3). Norma is the outlier, not the pioneer.
2. **The locale system deliberately does not translate module coordinates.**
   The done goal `library-locale-translation` states the boundary: "Local
   variables and canonical package/module import coordinates remain
   source-local and are not translated" (`radix/docs/factory/library-locale-translation/goal.md`,
   Goal boundary). The done norma conversion repeated it: "Import coordinates
   (`norma:*`) stay" (`norma/docs/factory/english-locale/goal.md`, Invariant).
   B is not a completion of that work — it is a reversal of its explicit
   boundary.
3. **The module coordinate is the one cross-package join key.** It is shared by
   producer layout, consumer imports, cista store paths, locale-pack
   `module_path` keys, docs, and grep. Making it dual-spelled taxes every one
   of those permanently (§6).

## 2. Census — the rename checklist

### 2.1 Module inventory (`norma/src/`, 31 module files) — known

26 top-level + 5 nested under `caelum/` (3), `json/` (4), `solum/` (1).
Latin-named: 17 top-level + 4 nested. Proposed English names (role from each
file's header comment; **operator-amendable table — ratify or amend before
units dispatch**):

| Latin coordinate | Proposed English | Role (from module header) |
| --- | --- | --- |
| `norma:aleator` | `norma:random` | entropy device (RNG) |
| `norma:arca` | `norma:database` | database device |
| `norma:caelum` | `norma:net` | network socket device (TCP/UDP) |
| `norma:caelum/auscultator` | `norma:net/listener` | socket listener |
| `norma:caelum/connexus` | `norma:net/connection` | socket connection |
| `norma:caelum/terminus` | `norma:net/endpoint` | socket endpoint |
| `norma:chorda` | `norma:text` | cord (string) operations |
| `norma:codex` | `norma:encoding` | encode/decode (base64/hex/url) |
| `norma:consolum` | `norma:console` | console device (stdin/out/err) |
| `norma:crypta` | `norma:crypto` | cryptography device |
| `norma:fila` | `norma:deque` | double-ended queue |
| `norma:mathesis` | `norma:math` | scalar math catalog |
| `norma:nuncius` | `norma:ipc` | inter-process communication device |
| `norma:ordinata` | `norma:ordered_map` | key-ordered associative collection |
| `norma:pressura` | `norma:compression` | compression device |
| `norma:processus` | `norma:process` | process device |
| `norma:solum` | `norma:fs` | filesystem device |
| `norma:solum/path` | `norma:fs/path` | pure pathname operations |
| `norma:tempus` | `norma:time` | datetime library |
| `norma:thesaurus` | `norma:cache` | cache + pub/sub device (dual role; split deferred — out of scope) |
| `norma:valor` | `norma:value` | codec-agnostic value-tree navigation |
| `norma:json/lexica` | `norma:json/lexer` | shared lexical floor |
| `norma:json/pange` | `norma:json/serialize` | serialization facade |

Already English/neutral — **unchanged**: `csv`, `http`, `json`, `model`,
`optimizer`, `tensor`, `toml`, `vector`, `yaml`, `json/cursor`, `json/solve`.

Naming notes: `chorda → string` was rejected for collision with the `string`
type name; `text` matches the type's en surface role without occupying the
type token. The package name `norma` itself stays — brand parallel to
`gradus`/`tela`/`triga` (Latin package names, English modules).

### 2.2 Live-code consumer census — known

Counting rule: occurrences of `norma:<path>` in `*.fab` / `*.proba` only
(docs counted separately in §2.3). Latin-rename-relevant rows only — neutral
names (`json`, `http`, `toml`, …) carry no rename cost:

| Repo | Latin-module refs | Detail |
| --- | --- | --- |
| `norma` (src self-imports + exempla) | ~90 (inferred from §2.4 mix) | exempla carry 61 import lines; src proba/self-imports the rest |
| `radix` fixture `.fab` | ~58 of 21 files' refs | `chorda` 9, `solum` 7 (+`solum/path` in .rs below), `tempus` 2, `valor` 1 |
| `radix` `.rs` fixture strings | ~113 | `chorda` 40, `solum` 39, `consolum` 10, `tempus` 8, `valor` 6, `processus` 6, `solum/path` 4 |
| `examples` | 36 | `solum` 18, `processus` 10, `consolum` 8 |
| `gradus` | 12 | `solum` 6, `processus` 6 — matches the operator's count exactly (exempla; the extra occurrences earlier seen are README/docs) |
| `hosts` | 0 | only `norma:tensor` (1) in live code — neutral name |
| `inferentia` | 0 | live code touches only `norma:json`, `norma:http` |
| `tela`, `triga`, `faber` | 0 | no live-code norma imports |

Total live-code Latin occurrences: roughly **230**, of which ~170 sit in
radix test fixtures. The operator's "mechanical but long-tail" framing is
confirmed — but the tail is dominated by radix's own fixture corpus, not by
product code.

### 2.3 Docs/config census — known

| Surface | Scale | Note |
| --- | --- | --- |
| `faberlang.dev` | ~700 refs across 311 files | largest single surface; also carries **stale** `norma:textus` imports (`src/en-US/cheatsheet/imports.md:47`) — a module that does not exist; doc drift already present |
| `norma` repo docs | ~330 refs | includes `docs/factory/` ledgers (historical — do not rewrite) |
| `radix` docs | ~600 refs | dominated by release notes + done-goal ledgers (historical — do not rewrite) |
| `faber/docs/EBNF.md` + faber docs | 23 refs | `chorda` 12, `json` 7, `consolum` 4 |
| locale packs | en 18 + la 96 `[[library_members]]` rows keyed by `module_path` | keys must move with the rename (same commit as the file rename) |
| `cista` | 6 files | install/package tests + archived docs |
| AGENTS.md files | `norma/AGENTS.md` rule text names `norma:solum`, `norma:solum/path` as canonical examples | instruction-file edit routed through mind/operator |

### 2.4 Hard compiler/store couplings — known

These are the non-mechanical spots a module rename touches in radix; each is a
small, named edit:

| Coupling | Evidence | Change under A |
| --- | --- | --- |
| carried-provider closed set | `radix/crates/radix-program/src/llvm.rs:383` matches `"solum" \| "tempus" \| "toml" \| "valor" \| "json"` | update `solum`→`fs`, `tempus`→`time`, `valor`→`value` |
| http module special case | `radix/crates/radix-program/src/library.rs:1557` (`module_path[0] == "http"`) | none (name unchanged) |
| pack `module_path` keys | `radix/stdlib/locale/{en,la}/pack.toml` | move keys to new coordinates |
| retired-surface rejection | `radix/scripta/check-retired-surfaces:91` (already rejects `norma:hal`) | add the old Latin spellings so live surfaces cannot regress |
| cista store layout | `$CISTAE_HOME/norma/<version>/interfaces/<module>/` (`norma/cista.toml` header) | none in-repo; installed stores need reinstall after upgrade (pre-1.0 acceptable) |
| hosts provider crates | `hosts/crates/{aleator,consolum,http,processus,solum,tempus}` + `radix/crates/faber/src/package/runtime_sources.rs` D1 table | **none — out of scope** (§8) |

## 3. Premise check: tela/gradus/triga module surfaces — known

- `gradus/src/`: `attention`, `cache`, `calibration`, `data`, `decode`,
  `dtype`, `generation`, `gradient`, `loss`, `math`, `metrics`, `nn`,
  `optimize`, `parameter`, `sampling`, `serialize`, `shape`, `tensor`,
  `tokenizer`, `train`, `transformer` — all English.
- `tela/src/`: `browser`, `canvas2d`, `dom`, `reference`, `validate`, `web` —
  all English.
- `triga/src/`: `geometry`, `graph`, `lighting`, `material`, `primitives`,
  `face`, `math` — all English.

The operator's premise is confirmed: Latin library *package* names with
English *module* surfaces is the established workspace shape; norma's Latin
module names are the inconsistency.

## 4. Locale-pack architecture today — known

The pack system (`radix/stdlib/locale/*/pack.toml`, loaded by
`radix/crates/faber/src/package/locale.rs` into
`radix_module::locale::LocalePack`) maps three token families plus library
members:

- `[keywords]` — Latin canonical → en surface (`functio = "fn"`); canonical
  identity stays Latin-keyed in HIR.
- `[types]` / `[intrinsics]` — same shape (`textus = "string"`,
  `longitudo = "length"`).
- `[[library_members]]` — **canonical is now English** (Pass B flipped members
  from Latin-canonical to English-canonical): en rows are identity
  (`canonical = "reverse"`, `surface = "reverse"`); the la pack carries 96 rows
  projecting Latin surfaces (`surface = "retorta"`).

**There is no module-name surface today.** `module_path = ["chorda"]` appears
only as the *lookup key* of a member row, never as a mapped value. The other
six packs (`zh-Hans`, `zh-Hant`, `ar`, `hi`, `vi`, `th-TH`) carry **zero**
`library_members` rows — relevant to B's containment claim (§6).

## 5. Evaluation

| Criterion | A — rename modules | B — locale-aware module names |
| --- | --- | --- |
| Consumer blast radius | ~230 live-code refs + docs sweep; dominated by radix fixtures; per-unit mechanical | zero consumer edits — the entire cost moves into radix |
| Single canonical spelling | preserved — one coordinate, one spelling, forever | **broken by design** — every module has ≥2 valid spellings (`norma:fs` / `norma:solum`), varying per file locale |
| grep / audit | one pattern finds everything | corpus splits across spellings; audits and the retired-surface check need alias-aware search |
| teach-ability | one name per concept; docs state it once | every doc example must pick a locale; learners meet both spellings in the wild |
| diagnostics | unambiguous module identity | renderer must choose a spelling; error text differs from what grep finds in other files |
| cista store / digests | unchanged (canonical path is the store path) | store stays canonical, source is surface — two vocabularies for one store object |
| alignment with settled rulings | completes Pass B's direction; LTL boundary ("coordinates are not translated") stands | reverses the LTL boundary and the norma english-locale invariant ("`norma:*` stay") |
| radix cost | small named edits (§2.4) inside existing units | new pack section + import-lowering canonicalization + pack plumbing through every entry point (§6) |
| la-reader experience | la keywords/types/members stay Latin; module coordinate is English (`importa ex "norma:string"`) | fully Latin reading experience |

The la-reader point is B's only genuine advantage, and it was already
weighed and settled: the LTL goal boundary and the norma english-locale
invariant both chose canonical, untranslated coordinates with locale-mapped
members. Reopening that for a cosmetic win is not justified by new evidence.

## 6. Why B is rejected — honest sizing and containment

What B would actually require in radix (**inferred** from the cited code; not
prototyped):

1. **Pack schema** — a `[[library_modules]]` section (surface ↔ canonical per
   package) with bidirectional-uniqueness validation and a completeness law.
   The completeness question has no good answer: en would carry identity rows,
   la Latin rows, and the other six packs — which today carry zero
   `library_members` rows — would need either an inheritance rule or
   fail-closed errors for their readers.
2. **Import lowering** — the surface spelling must canonicalize before HIR
   identity, because `(package, module_path)` feeds FileInterface lookup,
   interface digests, and re-export (`import … publica`). This is not just
   `resolve_library_provider_import` (`radix/crates/radix-module/src/import_resolve.rs:389`,
   currently a pure string→filesystem join); the pack must be plumbed through
   every entry point that resolves imports (faber package commands,
   radix-module CLI, proba runner, e2e harness).
3. **Formatter round-trip** — `radix::forma` must re-emit the file's surface
   spelling or `faber format` silently rewrites imports to canonical.
4. **Diagnostics rendering** — a spelling-choice rule per message.
5. **Effort** — M band (~150k–400k tokens) in radix alone, before the
   permanent dual-spelling tax on grep, docs, teaching, and audits.

**Containment verdict: not contained.** The operator's flagged danger is
structural, not incidental: the module coordinate is the join key between
producer layout, consumer imports, store paths, pack keys, generated
manifests, and docs. Aliasing it aliases all of them at once. The keyword and
member surfaces could be locale-mapped safely because they resolve to
identity *inside* one compilation; the module coordinate crosses compilation,
storage, and corpus boundaries. B is only recommendable if aliasing is
contained to display-only — and it cannot be, because the surface spelling is
what users type, grep, and ship.

## 7. Unit list (path A)

Sequenced; each unit is Hand-sized with path-limited commits. The census in
§2 is the checklist — a unit is done when its rows are exhausted for its
scope and the greps below (§7.1) are clean for that scope.

| Unit | Scope | Repo | Depends |
| --- | --- | --- | --- |
| **U0** | Ratify/amend the §2.1 name table | decision-only (operator) | — |
| **U1a** | Rename leaf modules (12): aleator, arca, chorda, codex, crypta, fila, mathesis, nuncius, ordinata, pressura, thesaurus, valor — files, self-imports, exempla, module headers | norma | U0 |
| **U1b** | Rename device families (6+sub): solum(+path), processus, tempus, consolum, caelum(+3) | norma | U0 |
| **U1c** | Rename json nested: lexica, pange | norma | U0 |
| **U2** | radix: pack `module_path` keys (en 18 + la 96 rows, same commit as U1), `llvm.rs:383` carried set, fixture `.fab` (21 refs) + `.rs` strings (~113), retired-surface rejection rows for old spellings | radix | U1a–U1c |
| **U3** | gradus live refs (12) + repo docs | gradus | U1 |
| **U4** | examples live refs (36) + READMEs | examples | U1 |
| **U5** | inferentia / hosts / tela docs (live code: 0 Latin refs) | inferentia, hosts | U1 |
| **U6** | faberlang.dev sweep (~700 refs) + fix the stale `norma:textus` cheatsheet imports while in there | faberlang.dev | U1 |
| **U7** | EBNF + faber docs (23 refs) | faber | U1 |
| **U8** | cista test fixtures (6 files) | cista | U1 |
| **U9** | Instruction surfaces: `norma/AGENTS.md` example coordinates, `$faber` skill references | norma / skills | U1 |
| **U10** | Closeout validation: stages 1–4 + one e2e fleet sweep (a cross-workspace coordinate rename justifies the fleet, not just per-target lanes) | — | U2–U9 |

Per-unit validation during implementation: `./scripta/check-source` (norma),
focused `cargo test -p <crate>`, `faber test .` for converted packages.
Historical records — `radix/docs/release/*`, done-goal ledgers,
`docs/archived/` — are **not** rewritten; history keeps the spellings it
shipped with.

### 7.1 Done-when oracle

For every live-code and live-doc scope (excluding release notes, archived
docs, and done-goal ledgers):

```sh
rg -n 'norma:(aleator|arca|caelum|chorda|codex|consolum|crypta|fila|mathesis|nuncius|ordinata|pressura|processus|solum|tempus|thesaurus|valor)' \
  <scope>   # must return 0 hits
```

…plus green stages 1–4 and the U10 e2e sweep, and `norma:hal`-style pointed
diagnostics for the old spellings (U2).

## 8. Non-goals / explicitly out of scope

- **Hosts provider crates and host route namespaces stay Latin.**
  `hosts/crates/{solum,tempus,consolum,processus,aleator}` and the `ad`-route
  families (`solum:*`, `tempus:*`, `http:*`) are hosts-repo ABI/product
  surface, internal to the device seam — they are not user-facing import
  coordinates. Consequence: `radix/crates/faber/src/package/runtime_sources.rs`
  D1 table needs no change. Aligning host-route names is a separate
  hosts-owned decision if ever wanted; recorded here as residual, not scope.
- Package names (`norma`, `gradus`, `tela`, `triga`) stay Latin — brand tier.
- `thesaurus` cache/pub-sub split, `caelum` directory-form redesign, and the
  http-module design (`docs/design/http-module.md`) are untouched; only the
  coordinate spelling changes.
- No compatibility aliases, no `la`-only Latin coordinates, no per-locale
  module spellings (that is B, rejected in §6).

## 9. Not claimed

- The §2.2/§2.3 counts are occurrence counts by `rg -o` at 2026-08-18 HEAD;
  they will drift with any landing — re-run the census greps at U0/U1 dispatch
  time and treat this memo's tables as the shape, not the immutable number.
- Proposed English names in §2.1 are defaults grounded in each module's
  header-comment role; final names are the operator's U0 call.
- B was sized by reading the import-resolution and pack code, not by
  prototyping; the M-band estimate is inferred, and the rejection does not
  rest on the estimate — it rests on the containment argument (§6), which is
  structural.
- I did not execute any conversion or test run; this is a read-only ruling
  with one uncommitted draft (this file).
