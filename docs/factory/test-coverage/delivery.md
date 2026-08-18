# Delivery — Norma module-wide proba coverage

**Status**: READY for audit.
**Planner**: planner. **Assignment**: `f99a96db`.
**Goal**: Norma English-locale residual `C1+` (proba coverage), tracked by
`docs/factory/english-locale/goal.md` §Residuals.
**Repo**: `norma` (direct on `main`); Radix/Faber is read-only evidence and
shared-defect routing.
**Pattern authority**: Norma `docs/factory/english-locale/pass-b-delivery.md`
(PB public-surface ledgers) and Radix `faber/docs/EBNF.md` plus the live proba
runner sources.

This document lowers the coverage scope only. It does not implement tests,
change Norma source visibility, or repair Radix/Faber.

---

## 1. Summary and readiness verdict

The live Norma source census is **34** `.fab` modules under `src/`. Only
`src/mathesis.proba` is colocated under `src/`, so the current colocated
coverage is **1/34**. The remaining scope is **33 module coverage rows**.

The existing row is not green:

```text
cd /Users/ianzepp/work/faberlang/norma
FABER_LIBRARY_HOME=/Users/ianzepp/work/faberlang \
  /Users/ianzepp/work/faberlang/radix/target/debug/faber \
  test src/mathesis.proba
```

Measured on the current trees and rebuilt Faber binary:

- exit `1`;
- six `SEM006.import_module_private` diagnostics;
- two cascading `SEM010` diagnostics;
- the six import failures are the three `mathesis.PI` and three
  `mathesis.add` references in the proba source;
- `faber check src/mathesis.fab` exits `0`.

**Verdict: READY for audit.** The delivery is grounded, but execution is
explicitly gated on two external conditions found by the triage below:

1. the defining module's intended public members must be exposed under the
   live visibility model; and
2. the Faber runner must execute calls into imported Norma source modules,
   rather than stopping at the provider-symbol boundary.

Neither condition is hidden inside a coverage Hand. The first is a Norma
visibility precondition. The second is an out-of-repo Radix/Faber shared unit.

---

## 2. Census and triage (`TC-00`)

### 2.1 Live census

The denominator is derived from the live tree, not the older decomposition
report:

```bash
find src -type f -name '*.fab' | sort | wc -l       # 34
find src -type f -name '*.proba' | sort             # mathesis.proba only
```

The 34 module paths are listed in the gate table in §8. The PB ledgers remain
the authority for the public member names and domain grouping:

- `PB-CHORDA`, `PB-CSV`, `PB-VALOR` — foundation family;
- `PB-CAELUM` — the four-file network family;
- `PB-ALEATOR`, `PB-FILA`, `PB-OPTIMIZER`, `PB-ORDINATA`, `PB-TENSOR`,
  `PB-VECTOR` — numeric and collection leaves;
- `PB-CODEX`, `PB-CRYPTA`, `PB-JSON`, `PB-TOML`, `PB-YAML`, `PB-PRESSURA` —
  codec and wire leaves;
- `PB-CONSOLUM`, `PB-PROCESSUS`, `PB-SOLUM`, `PB-TEMPUS` — I/O, process, and
  time leaves;
- `PB-MODEL` — model metadata;
- `PB-ARCA`, `PB-HTTP`, `PB-NUNCIUS`, `PB-THESAURUS` — storage, transport, and
  concurrency leaves.

The internal JSON files remain module rows because they are live source
modules, but their private helpers must be covered through the JSON facade
unless a later goal deliberately creates a test seam. A coverage row must not
make a private implementation helper public merely to obtain a direct import.

### 2.2 Visibility triage

Radix's live import seam says that an unmarked declaration and an explicit
private declaration are module-private. `@ public` is required for a cross-file
import. The source is authoritative at:

- `radix/crates/radix/src/semantic/passes/import_seam.rs`;
- `radix/crates/radix/src/semantic/passes/import_seam_test.rs`;
- `radix/docs/factory/visibility-model/delivery-phase1.md` §VM-U2/§VM-U4.

`src/mathesis.fab` currently has no `@ public` marker above `PI` or `add`.
The proba imports `./mathesis` from a separate `.proba` source, so the six
`SEM006.import_module_private` diagnostics are the expected visibility result.
This is not evidence of a stale binary or a broken locale pack.

The Vivi want `390d295c` is relevant but not a blanket fix. Its recorded scope
is the Norma visibility follow-up for the **caelum family, existing exempla,
and crypta-sha2** private-member calls after Pass B. It is therefore a
precondition for those named rows, but it does not expose the unmarked
mathesis catalog. The mathesis catalog needs its own Norma visibility closure
before its proba can import `PI` and `add`.

### 2.3 Controlled scratch proof

A bounded scratch copy was made outside the repository. Only `@ public` was
inserted above the two members used by `mathesis.proba`; the committed Norma
tree was not changed.

Results:

- the six `SEM006` import failures no longer blocked analysis;
- the test reached execution and reported three failures of
  `FaberScript unsupported: provider sym#...`;
- the same result occurred with an absolute package path and a temporary
  `faber.toml` package;
- a separate non-importing `src/inline.proba` control passed (`1 passed`).

This separates the two defects:

| Observation | Classification | Consequence |
| --- | --- | --- |
| `mathesis.proba` fails before execution with `SEM006.import_module_private` | Norma visibility precondition | Expose the intended public surface before claiming the row executable. `390d295c` is only a partial named precondition. |
| Visibility-fixed scratch reaches `FaberScript unsupported: provider sym#...` when calling imported `mathesis` functions | Radix/Faber runner/provider-link gap | Route out as `OUT-RADIX-PROBA-IMPORTED-PROVIDER`; do not weaken the executed-proba gate. |
| Inline non-importing proba passes | Runner control | The failure is not a generic inability to execute every proba case. |
| `faber test .` reports `cannot read '.'` on the live binary | Faber path defect, separately known | Use absolute module/package paths in this delivery. Route as `OUT-RADIX-FABER-TEST-CWD` if the canonical package gate still requires `.`. |

### 2.4 Triage unit

| Field | Value |
| --- | --- |
| `id` | `TC-00` |
| `outcome` | Durable visibility-vs-toolchain triage and explicit dependency routing for the Norma coverage wave. |
| `write_scope` | This delivery document only. No product source, `.proba`, Radix, or Faber edits. |
| `read_scope` | `norma/src/**`; `norma/src/mathesis.proba`; `norma/docs/factory/english-locale/pass-b-delivery.md`; `radix/crates/radix/src/semantic/passes/import_seam.rs`; `radix/crates/faber/src/commands/test.rs`; `radix/crates/radix/src/proba/mod.rs`; `faber/docs/EBNF.md`; current `gradus`/`triga` proba examples. |
| `depends_on` | None. |
| `done_when` | The baseline counts, exact SEM006/SEM010 classes, visibility proof, scratch provider failure, and both external routes are recorded. The document names `390d295c` as a narrow visibility precondition rather than misclassifying it as a toolchain fix. |
| `sanity` | Re-run the baseline command and the scratch/control comparison with a current rebuilt Faber binary. |
| `non_goals` | No `@ public` sweep; no Radix patch; no coverage authoring; no diagnostic suppression. |
| `risk` | Medium — a false green here would make every later executed-proba claim dishonest. |
| `integrable` | yes (planning artifact). |

---

## 3. Grammar and authoring contract

### 3.1 Language grammar

The authoritative grammar is `faber/docs/EBNF.md`:

```ebnf
assertStmt     := 'adfirma' expression ('secus' expression)?
probandumDecl  := 'probandum' STRING probaModifier* '{' probandumBody '}'
probaStmt      := 'proba' STRING probaModifier* blockStmt
probaModifier := 'omitte' STRING | 'futurum' STRING | 'solum' | 'tag' STRING
               | 'temporis' NUMBER | 'metior' | 'repete' NUMBER
               | 'fragilis' NUMBER | 'solum_in' STRING
```

The English locale maps the same grammar to the spellings used by the current
Norma source. The freshest local examples establish the authoring form:

- `norma/src/mathesis.proba` uses `import from "./mathesis" mathesis`, helper
  functions, `describe "..."`, `test "..."`, and `assert ...`;
- `gradus/src/math.proba` uses the current English source form with multiple
  `test` cases and typed assertions;
- `gradus/src/attention.proba` is the current large-suite example for helpers,
  typed error rendering, and exact-value/negative assertions;
- `norma/exempla/caelum/*.proba` is the current local example for imported
  module types and field assertions;
- `triga/src/math.proba` is an older Latin-surface example and is useful only
  as a historical shape reference, not as the current locale spelling.

### 3.2 Runner and file-boundary rules

The live runner implementation is in:

- `radix/crates/radix/src/proba/mod.rs` — inventory, lowering, selection, and
  MIR execution;
- `radix/crates/faber/src/commands/test.rs` — `faber test` dispatch,
  single-file `.proba` execution, and package test-source inclusion;
- `radix/crates/faber/src/package/proba_integration_test.rs` — the executable
  boundary tests for `.proba` discovery and import rejection.

The delivery follows these rules:

1. A `.proba` file is test-only. It is discovered by the test path and is not
   published as a Norma interface.
2. A `.proba` file may import its `.fab` implementation or public facade. It
   must never import another `.proba` file.
3. Shared helpers belong in `.fab` source, not in a test source imported by
   another test source.
4. Use `assert`/`adfirma` for proof. `nota` output is not a coverage oracle.
5. A `futurum` or `omitte` case may record a deferred API contract, but a
   module row is not executed-proof if every case is skipped.
6. A module with only deferred/provider-backed APIs must have a named
   deferred-case reason and an executed deterministic contract case where the
   source surface permits one. If no such case is possible, the row remains
   open and is routed rather than declared green.
7. Imported calls are not claimed executable until the provider-symbol runner
   route is fixed. Compile success is a separate evidence tier.
8. The gate uses a current rebuilt Faber binary and an absolute path. It does
   not use the known-broken `faber test .` invocation as its only proof.

### 3.3 Coverage minimum

Each direct module row must include:

- at least one positive assertion for each public function/type family in its
  PB ledger, with direct calls or construction where the surface permits;
- at least one boundary or negative assertion for every documented error,
  `null`, empty, invalid, or deferred path that is executable in the current
  source;
- explicit notes for public members that are deferred, provider-backed, or
  blocked by a compiler capability;
- no test-only visibility widening of private helpers.

The decomposition report is a prioritization input, not a replacement for the
PB ledgers. High-risk first cases include `solum`, `codex`, `crypta`, `chorda`,
`tempus`, `model`, `json`, and `valor`.

---

## 4. Normalized delivery outcome

After the coverage wave:

1. Every one of the 34 live source-module rows has a named proba evidence row.
2. The 33 rows without a colocated `src/**/*.proba` at the census boundary
   have new coverage sources, except implementation-only modules whose
   behavior is intentionally proven through the nearest public facade. Those
   rows are still explicit in the gate and may not disappear from the count.
3. The existing mathesis proba is repaired only as needed to satisfy the
   visibility and runner preconditions; its arithmetic scope is not silently
   widened while resolving infrastructure failures.
4. Public coverage follows the exact PB ledgers in
   `english-locale/pass-b-delivery.md`; no Latin-to-English rename is admitted
   in this delivery.
5. Every gate row records whether its proof is direct, facade-transitive,
   deferred, or blocked by an external toolchain unit.
6. `./scripta/check-source`, the per-module `faber test` sweep, and
   `git diff --check` are green at the final merge gate. A skipped or
   provider-failed row is not counted as executed green.

---

## 5. External shared units and preconditions

These are named dependencies, not Norma coverage Hands.

| id | owner / route | required result | relationship to this delivery |
| --- | --- | --- | --- |
| `NORMA-VIS-EXPOSE` | Norma visibility follow-up, Mind-routed; includes the exact intended public members for each PB ledger. The existing `390d295c` want is one named slice for caelum, exempla, and crypta-sha2. | Every public member imported by a coverage source is explicitly visible under the live import seam. Private helpers remain private. | Blocking precondition for direct imported-member analysis. `390d295c` is necessary for its named rows but is not sufficient for mathesis or the full 34-row set. |
| `OUT-RADIX-PROBA-IMPORTED-PROVIDER` | Radix/Faber shared unit; likely `radix` proba lowering/linking plus `faber` package test path. | A `.proba` that imports a visible Norma `.fab` module can lower and execute a call into that module. The scratch control must pass without `FaberScript unsupported: provider sym#...`. | Blocking precondition for the **executed** tier. Compile-only coverage may be authored in parallel, but cannot close the gate. |
| `OUT-RADIX-FABER-TEST-CWD` | Faber CLI package-test path. | `faber test .` resolves the current directory or the delivery defines an accepted absolute-path canonical gate. | Not needed for per-file absolute-path rows, but required if the workspace standard insists on `faber test .`. |
| `NORMA-RA1-RESIDUALS` | Existing Pass B/RA-1 residual routing from `english-locale/delivery.md` for mathesis, fila, ordinata, vector, and JSON parity. | No new semantic errors are attributed to a coverage proba. | Coverage authoring may proceed, but the row must distinguish source parity from test defects. |

No radix source edit is implied by the current SEM006 reproduction. If a
visibility-fixed minimal import still reports `SEM006.import_module_private`,
that is a new radix regression and must be attached to
`OUT-RADIX-PROBA-IMPORTED-PROVIDER` with the minimal repro, not fixed in a
Norma coverage file.

---

## 6. Ordered unit graph

`TC-00` is the triage receipt above. Once the visibility precondition is
accepted, the seven coverage batches have disjoint write scopes and may run in
parallel. The final gate is serial after all batches and external shared units.

```text
TC-00 triage
  ├─ NORMA-VIS-EXPOSE (external precondition)
  ├─ OUT-RADIX-PROBA-IMPORTED-PROVIDER (external precondition for execution)
  ├─ COV-FND  foundation                 (3 modules)
  ├─ COV-NET  caelum network             (4 modules)
  ├─ COV-NUM  numeric + collections      (6 modules)
  ├─ COV-WIRE codecs + JSON family       (7 modules)
  ├─ COV-IO   I/O + process + time       (5 modules)
  ├─ COV-META model + config             (3 modules)
  └─ COV-SYS  storage + transport        (5 modules)
       └─ TC-GATE per-module executed-proba sweep (34 rows)
```

All coverage batches depend on `TC-00` and the visibility decision. The
executed portion of each batch also depends on
`OUT-RADIX-PROBA-IMPORTED-PROVIDER`. `NORMA-RA1-RESIDUALS` is a read/dependency
for the five named parity modules, not a reason to weaken their tests.

### Common coverage-batch contract

For each `COV-*` unit:

- **write scope**: only the listed new or amended `src/**/*.proba` files;
- **read scope**: the corresponding PB ledger section, the module `.fab`
  files, existing relevant exempla, and the decomposition report;
- **forbidden scope**: Norma product source changes, visibility annotation
  changes, locale-pack edits, Pass B renames, Radix/Faber changes, and
  `scripta/` gate changes;
- **done_when**: each listed module has the gate row in §8 filled with direct
  or facade-transitive cases, all authored cases analyze without SEM006/SEM010
  caused by the batch, the executable cases pass once the external runner
  unit is green, deferred cases carry explicit modifiers/reasons, and
  `git diff --check` is silent;
- **sanity**: run the batch's narrow absolute-path proba commands and inspect
  the first failing diagnostic; do not convert a runner failure into a skip;
- **integrable**: yes for each batch after visibility edits and runner support
  are available; before those preconditions, the branch is planning-ready but
  not evidence-complete.

### `COV-FND` — foundation family

**Modules (3):** `chorda.fab`, `csv.fab`, `valor.fab`.
**PB ledgers:** `PB-CHORDA`, `PB-CSV`, `PB-VALOR`.
**Write scope:** `src/chorda.proba`, `src/csv.proba`, `src/valor.proba`.
**Risk:** medium; these are shared by the wire and consumer surfaces.

Authoring focus:

- `chorda`: assertion-based reverse/join/count/predicate/substring cases,
  byte encode/decode round trips, invalid-byte and frontmatter boundary
  cases; do not rely on the old output-only catch-all exemplum;
- `csv`: empty input, one row, multiple rows, CRLF normalization, embedded
  delimiter behavior as documented, and stringify/parse round trip;
- `valor`: all shape predicates with both matching and wrong-type values,
  map/list access, missing keys, out-of-bounds indexes, dotted paths, and
  strict extraction failures.

### `COV-NET` — caelum network family

**Modules (4):** `caelum.fab`, `caelum/terminus.fab`,
`caelum/connexus.fab`, `caelum/auscultator.fab`.
**PB ledger:** `PB-CAELUM`.
**Write scope:** `src/caelum.proba`, `src/caelum/terminus.proba`,
`src/caelum/connexus.proba`, `src/caelum/auscultator.proba`.
**Risk:** medium; the family has an internal type chain and the exact
`390d295c` visibility residual.

Authoring focus:

- endpoint construction and host/port field round trips;
- socket/listener type shape and method return contracts without opening an
  ambient network connection;
- deferred connect/listen/read/write/close cases recorded with `futurum`
  only where the current body is deliberately deferred;
- no test may claim a live network integration proof from a compile-only type
  assertion.

### `COV-NUM` — numeric and collection family

**Modules (6):** `aleator.fab`, `fila.fab`, `optimizer.fab`, `ordinata.fab`,
`tensor.fab`, `vector.fab`.
**PB ledgers:** `PB-ALEATOR`, `PB-FILA`, `PB-OPTIMIZER`, `PB-ORDINATA`,
`PB-TENSOR`, `PB-VECTOR`.
**Write scope:** `src/aleator.proba`, `src/fila.proba`,
`src/optimizer.proba`, `src/ordinata.proba`, `src/tensor.proba`,
`src/vector.proba`.
**Risk:** medium-high; generic and target-form residuals are known.

Authoring focus:

- aleator: seeded determinism where promised, range/shape checks, zero-length
  byte output, and UUID shape;
- fila: empty/single-element push/pop, both end directions, ordering,
  collect-to-list, and constructor type arguments;
- optimizer: same-shape update and shape mismatch behavior for the public SGD
  contract;
- ordinata: insertion order, get/has/delete, empty/from-map constructors,
  and delete/reinsert behavior;
- tensor: construct/flatten/product/apply/sum, empty-shape and empty-data
  boundaries, and f32 behavior;
- vector: add/subtract/multiply/dot/cross happy paths and shape/type
  boundaries, with the RA-1 `swizzle` removal respected;
- target-form or `mori`-only operations are named, not invoked as if they were
  executable.

### `COV-WIRE` — codec and JSON family

**Modules (7):** `codex.fab`, `crypta.fab`, `json.fab`,
`json/cursor.fab`, `json/lexica.fab`, `json/pange.fab`, `json/solve.fab`.
**PB ledgers:** `PB-CODEX`, `PB-CRYPTA`, `PB-JSON`.
**Write scope:** direct facade tests in `src/codex.proba`,
`src/crypta.proba`, `src/json.proba`, `src/json/pange.proba`,
`src/json/solve.proba`; implementation-only cursor/lexica rows are covered
through `src/json.proba` unless a public test seam is separately approved.
**Risk:** high; this family contains byte/text trust boundaries and deferred
wire APIs.

Authoring focus:

- codex: base64/hex/URL round trips and malformed-input behavior;
- crypta: SHA-2 known-answer vectors, algorithm rejection, input-size
  boundaries, and explicit contract rows for deferred HMAC/encryption/signing;
- JSON: stringify/parse round trips, malformed JSON, trailing commas,
  unterminated strings, escapes, and `try_parse` null behavior;
- cursor/lexica: every internal row must be traceable to a JSON facade case;
  do not export `_is_digit`, `_skip_whitespace`, or other private helpers just
  for test convenience;
- `json/pange` and `json/solve`: deferred runtime-dispatch cases are marked
  and remain execution-open until their provider path exists.

### `COV-IO` — I/O, process, and time family

**Modules (5):** `consolum.fab`, `processus.fab`, `solum.fab`,
`solum/path.fab`, `tempus.fab`.
**PB ledgers:** `PB-CONSOLUM`, `PB-PROCESSUS`, `PB-SOLUM`, `PB-TEMPUS`.
**Write scope:** `src/consolum.proba`, `src/processus.proba`,
`src/solum.proba`, `src/solum/path.proba`, `src/tempus.proba`.
**Risk:** high; file and process effects must not become ambient integration
claims.

Authoring focus:

- consolum: route/return contracts for read/write/TTY methods without making
  the suite depend on a real terminal;
- processus: argv/environment/cwd/identity contracts and explicit deferred
  process-spawn cases;
- solum: temporary-directory or fixture-scoped file operations, metadata,
  links, copy/rename/delete, glob, and async twin contracts; no writes outside
  a test-owned temporary root;
- solum/path: join/parent/file-name/extension/resolve with POSIX and boundary
  path strings;
- tempus: parse/render round trips, civil field extraction, invalid month/day/
  time inputs, clock/duration shape, and sleep/interval deferred contracts.

### `COV-META` — model and configuration family

**Modules (3):** `model.fab`, `toml.fab`, `yaml.fab`.
**PB ledgers:** `PB-MODEL`, `PB-TOML`, `PB-YAML`.
**Write scope:** `src/model.proba`, `src/toml.proba`, `src/yaml.proba`.
**Risk:** medium-high; model parsing has binary/text error paths and TOML/YAML
wire APIs are currently deferred.

Authoring focus:

- model: format detection, diagnostic construction, safetensors/GGUF happy
  paths, truncated/corrupt header negatives, invalid dtype/shape metadata,
  and exact error identity;
- TOML/YAML: parse/stringify/try-parse contract cases where bodies execute,
  malformed input and null/error-channel behavior where they do not;
- no fixture is promoted from a documentation example without an assertion.

### `COV-SYS` — storage, transport, and concurrency family

**Modules (5):** `arca.fab`, `http.fab`, `nuncius.fab`, `pressura.fab`,
`thesaurus.fab`.
**PB ledgers:** `PB-ARCA`, `PB-HTTP`, `PB-NUNCIUS`, `PB-PRESSURA`,
`PB-THESAURUS`.
**Write scope:** `src/arca.proba`, `src/http.proba`, `src/nuncius.proba`,
`src/pressura.proba`, `src/thesaurus.proba`.
**Risk:** medium; most bodies are provider/deferred contracts.

Authoring focus:

- arca: connection/transaction shape, query/execute/commit/rollback contract
  cases, and closed-connection negatives when implementation exists;
- HTTP: request/response fields, client method names, headers/body/status,
  and server route shape without external network I/O;
- nuncius: port pair, mutex, semaphore, and condition construction/contract
  cases, including timeout and closed-state edges;
- pressura: algorithm/level contract and stream lifecycle cases, with deferred
  calls explicitly marked;
- thesaurus: key/value, TTL, existence, increment/decrement, publish/subscribe
  contract cases and message field shape.

---

## 7. Unit field summary

The seven batch units are the Hand-sized logical changes. A batch is a family
because the proba authoring shares fixtures, import seams, and the PB ledger;
it is not permission to edit unrelated source or to hide a per-module gate.

| id | module count | exact write scope | depends_on | risk | integrable |
| --- | ---: | --- | --- | --- | --- |
| `COV-FND` | 3 | `src/chorda.proba`, `src/csv.proba`, `src/valor.proba` | `TC-00`, `NORMA-VIS-EXPOSE` | medium | yes |
| `COV-NET` | 4 | `src/caelum.proba`, `src/caelum/{terminus,connexus,auscultator}.proba` | `TC-00`, `NORMA-VIS-EXPOSE`, `390d295c` slice | medium | yes |
| `COV-NUM` | 6 | `src/{aleator,fila,optimizer,ordinata,tensor,vector}.proba` | `TC-00`, `NORMA-VIS-EXPOSE`, `NORMA-RA1-RESIDUALS` read | medium-high | yes |
| `COV-WIRE` | 7 | `src/codex.proba`, `src/crypta.proba`, `src/json.proba`, `src/json/{pange,solve}.proba`; cursor/lexica traced through `json.proba` | `TC-00`, `NORMA-VIS-EXPOSE`, `OUT-RADIX-PROBA-IMPORTED-PROVIDER` for execution | high | yes |
| `COV-IO` | 5 | `src/{consolum,processus,solum,tempus}.proba`, `src/solum/path.proba` | `TC-00`, `NORMA-VIS-EXPOSE`, `OUT-RADIX-PROBA-IMPORTED-PROVIDER` for execution | high | yes |
| `COV-META` | 3 | `src/{model,toml,yaml}.proba` | `TC-00`, `NORMA-VIS-EXPOSE`, `OUT-RADIX-PROBA-IMPORTED-PROVIDER` for execution | medium-high | yes |
| `COV-SYS` | 5 | `src/{arca,http,nuncius,pressura,thesaurus}.proba` | `TC-00`, `NORMA-VIS-EXPOSE`, `OUT-RADIX-PROBA-IMPORTED-PROVIDER` for execution | medium | yes |

No child unit owns `./scripta/check-source`, package-wide `faber test`,
`--stage`, `--e2e`, or Radix changes. Those belong to `TC-GATE` and the named
shared units.

---

## 8. Per-module executed-proba gate

This is the completion oracle. Every row must end in `GREEN` with at least one
executed case and the listed public-surface assertions, or in an explicitly
routed non-green state accepted by Mind as a residual. `futurum`/`omitte`
counts as a recorded deferred contract, not as an executed case.

The canonical command shape is:

```bash
ROOT=/Users/ianzepp/work/faberlang
FABER_BIN="$ROOT/radix/target/debug/faber"
export FABER_LIBRARY_HOME="$ROOT"
"$FABER_BIN" test "$ROOT/norma/<proba-path>"
```

`<proba-path>` is relative to the Norma root in the table. The `evidence`
column names the PB public-surface family that the row must cover.

| module | proba / executed evidence source | batch | evidence target | baseline |
| --- | --- | --- | --- | --- |
| `aleator.fab` | `src/aleator.proba` | `COV-NUM` | `PB-ALEATOR`: random float/int/bytes, UUID, seed | new |
| `arca.fab` | `src/arca.proba` | `COV-SYS` | `PB-ARCA`: connection and transaction contracts | new |
| `caelum.fab` | `src/caelum.proba` | `COV-NET` | `PB-CAELUM`: listen/connect public facade | new |
| `caelum/auscultator.fab` | `src/caelum/auscultator.proba` | `COV-NET` | `PB-CAELUM`: Listener/accept shape and deferred contract | new |
| `caelum/connexus.fab` | `src/caelum/connexus.proba` | `COV-NET` | `PB-CAELUM`: Socket read/write/close shape and deferred contract | new |
| `caelum/terminus.fab` | `src/caelum/terminus.proba` | `COV-NET` | `PB-CAELUM`: Endpoint construction and fields | new |
| `chorda.fab` | `src/chorda.proba` | `COV-FND` | `PB-CHORDA`: string and bytes public functions | new |
| `codex.fab` | `src/codex.proba` | `COV-WIRE` | `PB-CODEX`: base64/hex/URL codecs | new |
| `consolum.fab` | `src/consolum.proba` | `COV-IO` | `PB-CONSOLUM`: stream and TTY route contracts | new |
| `crypta.fab` | `src/crypta.proba` | `COV-WIRE` | `PB-CRYPTA`: digest vectors and security error boundaries | new |
| `csv.fab` | `src/csv.proba` | `COV-FND` | `PB-CSV`: parse/stringify rows and edge cases | new |
| `fila.fab` | `src/fila.proba` | `COV-NUM` | `PB-FILA`: Deque constructors and operations | new |
| `http.fab` + `http/{headers,request,response,chunked,sse,server,client}.fab` | `exempla/http/{headers,request,response,facade}.proba` | `COV-SYS` | H1 directory form: import + construct; method bodies need colocated runner support | landed construct fixtures |
| `json.fab` | `src/json.proba` | `COV-WIRE` | `PB-JSON`: facade parse/stringify/try-parse | new |
| `json/cursor.fab` | `src/json.proba` (transitive row) | `COV-WIRE` | `PB-JSON`: `ScanResult` cursor shape through JSON parser | new/transitive |
| `json/lexica.fab` | `src/json.proba` (transitive row) | `COV-WIRE` | `PB-JSON`: lexical helpers exercised through parse/stringify | new/transitive |
| `json/pange.fab` | `src/json/pange.proba` | `COV-WIRE` | `PB-JSON`: direct stringify facade contract | new |
| `json/solve.fab` | `src/json/solve.proba` | `COV-WIRE` | `PB-JSON`: direct parse/try-parse facade contract | new |
| `mathesis.fab` | existing `src/mathesis.proba` | `TC-00` + gate | `PB-MATHESIS`: PI/add now; catalog expansion stays scoped | existing, SEM006 + provider runner |
| `model.fab` | `src/model.proba` | `COV-META` | `PB-MODEL`: model metadata and binary/text negatives | new |
| `nuncius.fab` | `src/nuncius.proba` | `COV-SYS` | `PB-NUNCIUS`: port/mutex/semaphore/condition contracts | new |
| `optimizer.fab` | `src/optimizer.proba` | `COV-NUM` | `PB-OPTIMIZER`: SGD step and shape contract | new |
| `ordinata.fab` | `src/ordinata.proba` | `COV-NUM` | `PB-ORDINATA`: SortedMap ordering and constructors | new |
| `pressura.fab` | `src/pressura.proba` | `COV-SYS` | `PB-PRESSURA`: compression and stream contracts | new |
| `processus.fab` | `src/processus.proba` | `COV-IO` | `PB-PROCESSUS`: process/env/cwd contracts | new |
| `solum.fab` | `src/solum.proba` | `COV-IO` | `PB-SOLUM`: filesystem operations and glob | new |
| `solum/path.fab` | `src/solum/path.proba` | `COV-IO` | `PB-SOLUM`: path helper functions | new |
| `tempus.fab` | `src/tempus.proba` | `COV-IO` | `PB-TEMPUS`: civil time, invalid values, durations | new |
| `tensor.fab` | `src/tensor.proba` | `COV-NUM` | `PB-TENSOR`: tensor construction and reductions | new |
| `thesaurus.fab` | `src/thesaurus.proba` | `COV-SYS` | `PB-THESAURUS`: key/TTL/pub-sub contracts | new |
| `toml.fab` | `src/toml.proba` | `COV-META` | `PB-TOML`: parse/stringify/try-parse | new |
| `valor.fab` | `src/valor.proba` | `COV-FND` | `PB-VALOR`: shape/accessor/strict extraction contracts | new |
| `vector.fab` | `src/vector.proba` | `COV-NUM` | `PB-VECTOR`: vector arithmetic and shape boundaries | new |
| `yaml.fab` | `src/yaml.proba` | `COV-META` | `PB-YAML`: parse/stringify/try-parse/collections | new |

### Gate procedure (`TC-GATE`)

1. Verify the source set still contains exactly 34 `.fab` module paths and the
   gate table still has exactly 34 rows.
2. Verify each direct row's `.proba` file exists and each transitive row names
   an existing facade proba.
3. Run every row with the absolute `FABER_BIN` and
   `FABER_LIBRARY_HOME=$ROOT` setup. Record `passed`, `failed`, `skipped`, and
   the first diagnostic class.
4. Require zero `SEM006` and zero test-caused `SEM010` before execution.
5. Require at least one executed, passing case per row. A row with only
   `futurum`/`omitte`, a provider-symbol failure, or a compile-only check is
   not green.
6. Run `./scripta/check-source` and `git diff --check` from Norma after all
   proba files land.
7. Re-run the full row sweep after any visibility or runner repair. Do not
   convert an external red to a skipped case to preserve green.

The gate is intentionally stronger than “33 files exist.” It proves that the
coverage is discovered, semantically importable, and actually executed by the
canonical runner.

---

## 9. Validation ownership and stop conditions

### Lane-owned validation

- **Hand/batch sanity:** one narrow absolute-path `faber test` per touched
  module plus `git diff --check`. A Hand reports runner/provider failures
  without editing Radix or weakening the case.
- **Lint lane:** `./scripta/check-source` and mechanical source-library gates
  after the proba files land.
- **Test lane:** `TC-GATE`'s 34-row executed sweep, including all deferred and
  transitive classifications; the test lane owns the final evidence table.
- **Merge lane:** integrates disjoint batch commits, verifies the 34-row gate
  table remains complete, and runs `scripta/verify-main-consistent` for Norma.
- **Radix/Faber owners:** close `OUT-RADIX-PROBA-IMPORTED-PROVIDER` and, if
  required by the canonical command, `OUT-RADIX-FABER-TEST-CWD`.

### Stop conditions

Stop and route rather than expanding scope when:

- a coverage author needs to add or change `@ public`/`@ private` in Norma
  source;
- a private JSON helper would need to be exported solely for a direct test;
- an API is only a deferred/provider contract and has no executable case;
- a proba fails with `SEM006` after the intended public annotations are present;
- the runner fails with provider-symbol or MIR-lowering errors;
- a fixture requires ambient network, process, filesystem, or terminal state;
- a PB ledger member is absent or changed relative to live source.

These are findings or dependencies, not permission to weaken the gate.

---

## 10. Open questions for audit

1. **Visibility closure boundary:** Should `NORMA-VIS-EXPOSE` be one Norma
   annotation wave for all PB public members, or should Mind sequence it by the
   seven coverage families? Default: one visibility precondition before direct
   proba execution, with family-sized commits.
2. **Internal JSON rows:** Confirm the default that `json/cursor` and
   `json/lexica` are facade-transitive rows and do not export private helpers.
3. **Deferred-only modules:** Confirm the minimum accepted executed contract
   for modules whose public bodies are all `mori`, `ad`, or provider-backed.
   Default: one deterministic construction/shape case where possible;
   otherwise keep the row explicitly open until the provider exists.
4. **Runner repair ownership:** Confirm that the provider-symbol failure is
   filed as `OUT-RADIX-PROBA-IMPORTED-PROVIDER`, not patched in Norma proba
   sources.
5. **Canonical package command:** Confirm whether the final gate is the
   absolute per-file sweep in §8 or a repaired package-root `faber test`.
   Default: absolute per-row sweep is the evidence oracle; package-root test is
   an additional shared-tool check.

---

## 11. Delivery report

```text
kind: delivery
planner: planner
assignment: f99a96db
goal_path: norma/docs/factory/english-locale/goal.md (§Residuals C1+)
delivery_path: norma/docs/factory/test-coverage/delivery.md
verdict: READY
census: 34 src modules; 1 existing colocated proba; 33 coverage rows
triage: SEM006 is the expected visibility seam; want 390d295c is a narrow
        precondition for its named residuals, not a mathesis/toolchain fix;
        visibility-fixed scratch then exposes an out-of-repo provider-symbol
        runner defect
batch_count: 7 coverage batches + TC-00 triage + TC-GATE
external_units:
  - NORMA-VIS-EXPOSE
  - OUT-RADIX-PROBA-IMPORTED-PROVIDER
  - OUT-RADIX-FABER-TEST-CWD
```
