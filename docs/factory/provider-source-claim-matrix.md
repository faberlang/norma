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
- A module may be named as public source shape when the claim is about
  signatures, native Faber bodies, codegen templates, `ad` route intent, or
  explicit `mori` deferral state.
- `aleator`, `consolum`, `processus`, `solum`, and `tempus` may be described as
  route families that have provider-manifest/dispatch evidence in the host
  audit, but not as public runnable support.

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
| Pure/native source helpers | `csv`, `fila`, `json`, `json/*`, `mathesis`, `model`, `ordinata`, `tensor`, `valor`, `vector`; native portions of `chorda` and `tempus` | Native Faber bodies and compiler/runtime conversions where present | Source-level helper surface exists and can be audited in this repo | Broad runtime product claim without focused compile/run evidence |
| Host-provider route evidence, not public support | `aleator:*`, `consolum:*`, `processus:*`, `solum:*`, `tempus:*` | Prior host-gateway audit records host-provider manifests for `aleator`, `consolum`, `processus`, `solum`, and `tempus`; Norma source has matching `ad` route families | Route intent and provider-evidence candidate families | Public support matrix, runnable examples, or parity claims before export/run evidence |
| Partial filesystem/process routes | `solum`, `solum/path`, `processus` | Many `ad` routes exist; `solum.describe`, `solum.describet`, `solum.fundet`, and `processus.genera` remain deferred | Partial source route surface with named materializer blockers | Complete filesystem metadata, byte-write async parity, or process lifecycle API |
| Partial time routes | `tempus` | Clock/sleep `ad` routes exist; `vigila` remains deferred because live inbound cursor returns from functions are not available | Source route surface for clocks and one-shot waits | Timer stream / cursor support |
| Deferred host-effect source shape | `arca`, `caelum`, `caelum/*`, `crypta`, `http`, `nuncius`, `pressura`, `thesaurus` | Public signatures and comments exist; bodies are `mori "norma:... deferred pending Stage 2 dispatch"` | Planned source shape only | Provider support, host gateway support, network/database/crypto/IPC/cache/compression runtime claims |
| Deferred codec/mechanical routes | `codex`, `toml`, `yaml`; deferred portions of `chorda` | Public signatures exist; bodies are `mori` deferrals | Source facade or planned wire floor only | Encoding/TOML/YAML runtime behavior without conversion/provider evidence |
| Deferral mechanism | modules with pure `mori "norma:..."` stubs | `nondum-mori-deferral-residual.md` records free-function `@ nondum` as blocked on Radix SEM017 support | Runtime deferrals are known debt | Compile-time unavailability, unless Radix free-function `@ nondum` support lands |

## Promotion Triggers

Promote a module from source-shape or provider-candidate status only when all
matching evidence exists:

| Promotion | Required evidence |
| --- | --- |
| Public source reference | This repo's source policy passes under `./scripta/check-source`; docs name deferred stubs honestly. |
| Provider coverage claim | Provider manifest export exists for the route family, dispatch coverage is validated, and public contract output is regenerated from that export rather than hand-written. |
| Runnable example claim | A public example imports the Norma module, runs through the released compiler/package path, and exercises the host route without private setup. |
| Complete module claim | Every public function in that module is either native/generated and exercised, or has provider evidence; no `mori` deferral remains in the claimed surface. |
| Cross-host or production claim | At least two host backends or an explicit target matrix pass the same example and negative cases; unsupported routes fail closed. |
| Deferral cleanup claim | Radix free-function `@ nondum` SEM017 support exists and the module has been migrated away from runtime-only `mori` stubs. |

## Validation Commands

For this docs-only matrix:

```bash
git diff --check
./scripta/check-source
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
- Complete `solum` metadata and async byte-write claims.
- Complete `processus` lifecycle claims.
- Timer-stream cursor support through `tempus.vigila`.
- Any public crypto, IPC, HTTP, network, database, cache, compression, TOML,
  YAML, codex, or deferred `chorda` runtime support claim.
- Cross-host parity or production-ready host backend behavior.
