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
  explicit `mori` deferral state.
- `aleator`, `consolum`, `processus`, `solum`, and `tempus` may be described as
  route families with local provider manifest/dispatch coverage evidence in
  `host-providers-rs` commit `bed50e5`, but not as public runnable support.

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
| Compile/import-evidenced native helpers | `csv`, `json`, `json/pange`, `json/solve`, `json/cursor`, `json/lexica`, `model`, `tensor`, `valor`, `vector`, `solum/path`; native portions of `chorda` and `tempus` | `./scripta/check-promoted-helper-imports` runs `faber check` import smoke for each listed `norma:*` module using this repo as `FABER_LIBRARY_HOME` | These helper surfaces have local compile/import evidence as source modules | Runtime product behavior, complete module behavior, or public run claims without focused run evidence |
| Parked target-form helper surfaces | `fila`, `ordinata` | Module headers self-declare target form. `fila` waits on generic genus construction; `ordinata` waits on generic genus construction plus ordered-key bounds and a planned `tabula.claves()` intrinsic. These modules are intentionally not included in promoted import smoke. | Parked public source shape only; surface is available for review as target-form design | Compile/import-evidenced helper, runnable collection API, complete collection helper, or public example claim |
| Compile-gap native helper surfaces | `mathesis` | Native source exists, but current import smoke fails before this packet's promotion threshold; keep out of promoted rows until a focused compiler/source fix lands and `check-promoted-helper-imports` covers it. | Source shape and local audit only | Compile/import-evidenced helper or runtime behavior claim |
| Host-provider route evidence, not public support | `aleator:*`, `consolum:*`, `processus:*`, `solum:*`, `tempus:*` | `host-providers-rs` commit `bed50e5` records local manifest/dispatch coverage with no manifest route missing from Rust dispatch strings: `aleator` 5 routes, `consolum` 16, `processus` 9 provider-covered routes, `solum` 45, `tempus` 4. Norma source has matching `ad` route families for the overlapping surfaces, and `./scripta/audit-provider-route-claims` verifies source/manifest parity plus manifest-to-dispatch route-string coverage against that expected sibling provider revision only when the sibling checkout/index is clean. `processus:exi` is explicitly source-only/deferred and not provider-covered. | Local route intent plus provider manifest/dispatch coverage evidence for manifested routes; `processus:exi` may be named only as a source-level never-returning exit intent excluded from provider coverage | Public support matrix, runnable examples, package/install support, or parity claims before export/run evidence; provider coverage for `processus:exi` |
| Partial filesystem/process routes | `solum`, `solum/path`, `processus` | Many `ad` routes exist; `solum.describe`, `solum.describet`, `processus.genera`, and source-only `processus:exi` remain deferred or excluded from provider coverage for distinct reasons. `solum.fundet` now routes through canonical `solum:funde`; package/run evidence still gates public async byte-write claims. `processus.genera` is blocked on `Subprocessus` materialization; `processus:exi` is excluded until host exit has a protocol-visible terminal response. | Partial source route surface with named materializer and provider-coverage blockers | Complete filesystem metadata, public byte-write async parity, process lifecycle API, or provider-covered process exit |
| Partial time routes | `tempus` | Clock/sleep `ad` routes exist; `vigila` remains deferred because live inbound cursor returns from functions are not available | Source route surface for clocks and one-shot waits | Timer stream / cursor support |
| Pure network source type | `caelum/terminus` | `src/caelum/terminus.fab` defines the concrete `Terminus` genus with `hospes` and `portus` fields and no deferred host-effect body | Source type shape only | Runnable networking, socket endpoint resolution, or provider-backed transport behavior |
| Deferred host-effect source shape | `arca`, `caelum`, `caelum/auscultator`, `caelum/connexus`, `crypta`, `http`, `nuncius`, `pressura`, `thesaurus` | Public signatures and comments exist; bodies are `mori "norma:... deferred pending Stage 2 dispatch"` | Planned source shape only | Provider support, host gateway support, network/database/crypto/IPC/cache/compression runtime claims |
| Deferred codec/mechanical routes | `codex`, `toml`, `yaml`; deferred portions of `chorda` | Public signatures exist; bodies are `mori` deferrals | Source facade or planned wire floor only | Encoding/TOML/YAML runtime behavior without conversion/provider evidence |
| Deferral mechanism | 81 exact deferred `mori "norma:... deferred pending Stage ..."` stubs across 16 source files | `nondum-mori-deferral-residual.md` records `./scripta/audit-deferred-mori` output and keeps free-function `@ nondum` blocked on Radix SEM017 support | Runtime deferrals are known debt | Compile-time unavailability, unless Radix free-function `@ nondum` support lands |

## Promotion Triggers

Promote a module from source-shape or provider-candidate status only when all
matching evidence exists:

| Promotion | Required evidence |
| --- | --- |
| Public source reference | This repo's source policy passes under `./scripta/check-source`; docs name deferred stubs honestly. |
| Compile/import-evidenced native helper | Add the module to `scripta/check-promoted-helper-imports` and keep the matrix row in sync with the script. Promoted helper rows must be backed by a passing `faber check` import smoke. |
| Local provider-coverage evidence | A repo-local provider packet, such as `host-providers-rs` `bed50e5`, shows manifest/dispatch agreement and local tests for the route family. Norma's route audit must also pass against a clean sibling provider workspace at the expected revision, including its missing-dispatch, dirty-provider-evidence, and revision-mismatch negative self-tests. This permits private-preview evidence wording only. |
| Public provider coverage claim | Provider manifest export exists for the route family, dispatch coverage is validated, and public contract output is regenerated from that export rather than hand-written. |
| Runnable example claim | A public example imports the Norma module, runs through the released compiler/package path, and exercises the host route without private setup. |
| Complete module claim | Every public function in that module is either native/generated and exercised, or has provider evidence; no `mori` deferral remains in the claimed surface. |
| Cross-host or production claim | At least two host backends or an explicit target matrix pass the same example and negative cases; unsupported routes fail closed. |
| Deferral cleanup claim | Radix free-function `@ nondum` SEM017 support exists and the module has been migrated away from runtime-only `mori` stubs. |

## Validation Commands

For this docs-only matrix:

```bash
git diff --check
./scripta/check-source
./scripta/check-promoted-helper-imports
./scripta/audit-deferred-mori
./scripta/audit-provider-route-claims
./scripta/audit-provider-route-claims --self-test
python3 -m py_compile scripta/audit-provider-route-claims
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
