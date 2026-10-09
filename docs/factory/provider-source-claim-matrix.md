# Provider / Source-Library Claim Matrix

**Status:** claim gate recorded; docs-only P1 preview honesty packet
**Date:** 2026-07-14
**Scope:** Norma public source-library claims, provider route evidence, and
blocked public runnable claims. This matrix does not change source/API behavior.

## Rule

Norma may be described as the public backend-agnostic `norma:*` source library.
Do not describe a Norma module or route family as publicly runnable, provider
covered, exported, packaged, or host-portable until the matching trigger below
has landed with public evidence.

Allowed today:

- `src/**/*.fab` is the canonical public source-library surface.
- `src/` is the cista package interface root and is kept `.fab`-only by
  `./scripta/check-source`.
- A module may be named as public source shape when the claim is about
  signatures, native Faber bodies, codegen templates, `ad` route intent, or
  explicit deferral state (`@ unstable` declarations).
- `aleator`, `consolum`, `processus`, `solum`, and `tempus` may be described as
  route families with local provider manifest/dispatch coverage evidence in
  `faberlang/hosts` commit `e066ee0`, but not as public runnable support.

Blocked today:

- runnable Norma host-effect APIs;
- exported provider manifests as a support matrix;
- public examples whose success depends on host gateway dispatch;
- package/install claims requiring provider export or host dispatch;
- cross-host parity, production readiness, or full module reference claims.

## Matrix

| Claim family | Modules / routes | Current evidence | Allowed claim | Blocked claim |
| --- | --- | --- | --- | --- |
| Public source shape | all `src/**/*.fab` modules | Canonical Norma source repo; `README.md` and `AGENTS.md` define the rules | Norma is a public source library with backend-agnostic Faber modules | Runnable standard library, packaged provider support, or installable app examples |
| Compile/import-evidenced native helpers | `csv`, `json`, `json/solve`, `json/cursor`, `json/lexer`, `model`, `tensor`, `value`, `vector`, `fs/path`; native portions of `text` and `time` (post-R100 English names, re-synced 2026-09-17) | `faber run` on a manifest-free staged copy of `scripta/check-promoted-helper-imports.fab`, invoked from the repo root (kernel-script surface: the in-tree path sits below `faber.toml` and routes to package mode, which rejects `faber:*` kernel imports) runs a `faber check` import smoke for each listed `norma:*` module and fails closed when a listed module does not resolve in the live corpus (`norma/src/<module>.fab`), using the workspace as `FABER_LIBRARY_HOME` | These helper surfaces have local compile/import evidence as source modules | Runtime product behavior, complete module behavior, or public run claims without focused run evidence |
| Parked target-form helper surfaces | `fila`, `ordinata` | Module headers self-declare target form. `fila` waits on generic genus construction; `ordinata` waits on generic genus construction plus ordered-key bounds and a planned `tabula.claves()` intrinsic. These modules are intentionally not included in promoted import smoke. | Parked public source shape only; surface is available for review as target-form design | Compile/import-evidenced helper, runnable collection API, complete collection helper, or public example claim |
| Closed compile-gap native helper surfaces | `math`; `json/serialize` (post-R100 English names of `mathesis`/`json/pange`) | Gap closed 2026-09-17; the recheck predicate is met — json parse/serialize is now pure Faber (norma `65eb8d2`, `d6474af`), so check mode no longer needs the compiler-owned json→textus conversio. Import smoke re-run 2026-09-17 at norma `d6474af` with `FABER_LIBRARY_HOME=<workspace>`: `faber check` on a temp file `import from "norma:math" m` / `import from "norma:json/serialize" m` + `main { }` exits 0 for both, with only benign `WARN002 unused_import`. History: re-tiered compile-gap 2026-08-09 (SEM016; head-cto disposition on need 0a1812a5); the 2026-08-25 R100 rename (`5fc2645`) deleted the Latin names while the gap was open. Not yet wired into the `check-promoted-helper-imports` promoted smoke list (that promotion is a follow-up list addition plus a promoted-row sync). | Compile/import-evidenced helper surface as source modules, on the recorded row evidence above | Runtime product behavior, complete module behavior, or public run claims without focused run evidence |
| Host-provider route evidence, not public support | `aleator:*`, `consolum:*`, `processus:*`, `solum:*`, `tempus:*` | `faberlang/hosts` commit `e066ee0` records local manifest/dispatch coverage with no manifest route missing from Rust dispatch strings: `aleator` 5 routes, `consolum` 16, `processus` 9 provider-covered routes, `solum` 45, `tempus` 4. Norma source has matching `ad` route families for the overlapping surfaces, and `faber script scripta/audit-provider-route-claims.fab` verifies source/manifest parity plus manifest-to-dispatch route-string coverage against that expected sibling provider revision only when the sibling checkout/index is clean. `processus:exi` is explicitly source-only/deferred and not provider-covered. | Local route intent plus provider manifest/dispatch coverage evidence for manifested routes; `processus:exi` may be named only as a source-level never-returning exit intent excluded from provider coverage | Public support matrix, runnable examples, package/install support, or parity claims before export/run evidence; provider coverage for `processus:exi` |
| Partial filesystem/process routes | `solum`, `solum/path`, `processus` | Many `ad` routes exist; `solum.describe`, `solum.describet`, `processus.genera`, and source-only `processus:exi` remain deferred or excluded from provider coverage for distinct reasons. `solum.fundet` now routes through canonical `solum:funde`; package/run evidence still gates public async byte-write claims. `processus.genera` is blocked on `Subprocessus` materialization; `processus:exi` is excluded until host exit has a protocol-visible terminal response. | Partial source route surface with named materializer and provider-coverage blockers | Complete filesystem metadata, public byte-write async parity, process lifecycle API, or provider-covered process exit |
| Partial time routes | `tempus` | Clock/sleep `ad` routes exist; `vigila` remains deferred because live inbound cursor returns from functions are not available | Source route surface for clocks and one-shot waits | Timer stream / cursor support |
| Pure network source type | `caelum/terminus` | `src/caelum/terminus.fab` defines the concrete `Terminus` genus with `hospes` and `portus` fields and no deferred host-effect body | Source type shape only | Runnable networking, socket endpoint resolution, or provider-backed transport behavior |
| Pure HTTP genera (directory form) | `http/headers`, `http/request`, `http/response` | H1: `src/http/{headers,request,response}.fab` — case-insensitive multi-value headers, request-target split + query decode, response status classes. Native Faber, no `http:*` routes | Source type shape; `faber check` + exempla construct | Runnable HTTP I/O, provider-backed client/server, or streaming claims |
| Deferred HTTP codecs | `http/chunked`, `http/sse` | H1 structural leaves; bodies are H2 `mori` stubs | Source shape only | Chunked/SSE runtime behavior |
| Deferred host-effect source shape | `arca`, `caelum`, `caelum/auscultator`, `caelum/connexus`, `crypta`, `http`, `http/server`, `http/client`, `pressura`, `thesaurus` | Public signatures and comments exist; client bodies are `mori "norma:... deferred pending Stage 2 dispatch"`; server bodies are `call 'http:*'` wrappers | Planned source shape only | Provider support, host gateway support, network/database/crypto/IPC/cache/compression runtime claims |
| Deferred codec/mechanical routes | `codex`, `toml`, `yaml`; deferred portions of `chorda` | Public signatures exist; bodies are `mori` deferrals | Source facade or planned wire floor only | Encoding/TOML/YAML runtime behavior without conversion/provider evidence |
| Deferral mechanism | 58 bodiless `@ unstable "deferred pending Stage 2 dispatch"` free-function declarations across 12 source files; 11 class-method stubs in `src/net/connection.fab` and `src/net/listener.fab` still `panic` at run time (counts as of 2026-10-09; `rg 'deferred pending Stage' src` lists them) | Radix SEM017 `nondum_function_for_target` refuses any use of an `@ unstable` function at compile time (norma `56f8312`, radix `1fa1ad8e3`) | Free-function deferrals are known debt that fails at compile time; a remaining run-time `panic` stub is a defect to convert to the `@ unstable` form | Any runtime behavior of a deferred function |

## Promotion Triggers

Promote a module from source-shape or provider-candidate status only when all
matching evidence exists:

| Promotion | Required evidence |
| --- | --- |
| Public source reference | This repo's source policy passes under `./scripta/check-source`; docs name deferred stubs honestly. |
| Compile/import-evidenced native helper | Add the module to `scripta/check-promoted-helper-imports.fab` and keep the matrix row in sync with the script. Promoted helper rows must be backed by a passing `faber check` import smoke. |
| Local provider-coverage evidence | A repo-local provider packet, such as `faberlang/hosts` `e066ee0`, shows manifest/dispatch agreement and local tests for the route family. Norma's route audit must also pass against a clean sibling provider workspace at the expected revision, including its missing-dispatch, dirty-provider-evidence, and revision-mismatch negative self-tests. This permits private-preview evidence wording only. |
| Public provider coverage claim | Provider manifest export exists for the route family, dispatch coverage is validated, and public contract output is regenerated from that export rather than hand-written. |
| Runnable example claim | A public example imports the Norma module, runs through the released compiler/package path, and exercises the host route without private setup. |
| Complete module claim | Every public function in that module is either native/generated and exercised, or has provider evidence; no deferred stub (`@ unstable` declaration or run-time `panic` body) remains in the claimed surface. |
| Cross-host or production claim | At least two host backends or an explicit target matrix pass the same example and negative cases; unsupported routes fail closed. |
| Deferral cleanup claim | The module has no run-time `panic` stub left: every deferral is a bodiless `@ unstable` declaration that SEM017 refuses at compile time, or has been given a real body. |

## Validation Commands

For this docs-only matrix:

```bash
git diff --check
./scripta/check-source
stage=$(mktemp -d) && cp scripta/check-promoted-helper-imports.fab "$stage/"
FABER_LIBRARY_HOME="$(pwd)/.." faber run "$stage/check-promoted-helper-imports.fab"
faber script scripta/audit-provider-route-claims.fab
faber script scripta/audit-provider-route-claims.fab -- --self-test
```

For a future promotion packet, add the relevant public evidence command, such
as a released `faber check` / `faber run` example, provider manifest export
validation, or package contract regeneration. A promotion packet must name the
exact command and artifact it uses as evidence.

## Remaining Blocked Claims

- Complete `/norma` public reference or "Norma in practice" tutorial.
- Public provider coverage matrix generated from exported manifests.
- Runnable filesystem, process, console, random, and timer examples through the
  public package path.
- Complete `solum` metadata claims and public async byte-write claims.
- Complete `processus` lifecycle claims.
- Timer-stream cursor support through `tempus.vigila`.
- Any public crypto, IPC, HTTP, network, database, cache, compression, TOML,
  YAML, codex, or deferred `chorda` runtime support claim.
- Cross-host parity or production-ready host backend behavior.
