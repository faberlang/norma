# Host Gateway / Norma Claim-Gate Audit

**Status:** audited P1, defer implementation unless public claims change
**Date:** 2026-07-14
**Scope:** current `faberlang.dev` RC1/private-preview claims, Norma public source
library evidence, and public host gateway/provider evidence.

## Recommendation

Keep Host Gateway/Norma claim-gate work at **P1** for RC1. The current public
packet does not depend on a shipped Host Gateway or runnable Norma host-effect
claim. It names Norma only as an external/default library direction and keeps
public source, runnable examples, package registry, provider exports, and host
backend product claims behind explicit gates.

Promote to **P0** only if public copy or release assets start claiming any of
the following:

- runnable Norma APIs such as `norma:solum`, `norma:processus`,
  `norma:consolum`, `norma:aleator`, or `norma:tempus`;
- a complete `/norma` reference or "Norma in practice" tutorial backed by host
  effects, async examples, cursors, filesystem, process, console, timer, crypto,
  or IPC behavior;
- exported provider manifests as public truth for supported effects;
- install/package examples whose success requires host gateway route dispatch;
- cross-host parity or production-ready host backend behavior.

## Public Claim Surface Inspected

Current RC1 site assets keep the relevant claims local and gated:

| Surface | Current claim | Host Gateway dependency |
| --- | --- | --- |
| `assets/index.html` | Local RC1 evaluation preview; native app and package-binary lanes are locally evidenced; systems, accelerated, and broader backend lanes are tracked, not promised. | None for public release because host backend claims are explicitly not product claims. |
| `assets/llms.txt` | Hard gates block installability, public source builds, live registry, production readiness, and runnable examples without public source and run evidence. | None until runnable example/provider claims are added. |
| `assets/contracts/1.0.0-rc.1/providers.json` | Placeholder contract route says route lists will be populated from provider manifest export. | Future dependency only; placeholder is a blocker, not public evidence. |
| `docs/stage-1-public-contract-matrix.md` | Norma/Triga/external projects may be named with public repos/docs; feature claims require hard evidence. | P1 evidence gap, not RC1 P0. |
| `docs/stage-0-positioning-brief.md` | "Norma in practice" is a future post concept requiring `norma:json` and async exempla; no public repo/install claim yet. | Promotion trigger if moved into current public copy. |

Safe current wording: Norma can be described as an external/default standard
library direction with its own source/docs/release gate. Unsafe current wording:
Norma host-effect APIs are runnable, host gateway is shipped, provider routes
are a public support matrix, or `/norma` reference/tutorial pages are complete.

## Norma Evidence

`norma/README.md` defines Norma as the public backend-agnostic Faber source
library under `src/**/*.fab`. It explicitly allows native Faber bodies,
codegen templates, `ad` calls, and explicit `mori` deferrals, and says package
store consumption is still not the local development path.

Current Norma source already routes many host-effect functions through `ad`, but
there are important deferred public functions whose comments name gateway or
materializer blockers:

| Module | Evidence | Claim impact |
| --- | --- | --- |
| `src/solum.fab` | Many scalar/list filesystem routes use `ad 'solum:*'`; `describe` and `describet` still `mori` because arbitrary genera such as `SolumStatus` are not supported by the `ad` materializer. | Do not claim complete filesystem metadata API. |
| `src/processus.fab` | Process routes such as `dimitte`, `captura`, `exsequi`, and environment calls use `ad 'processus:*'`; `genera` is deferred because `Subprocessus` cannot be materialized yet. `processus:exi` is a source-only never-returning exit intent and is excluded from provider coverage until host exit has a protocol-visible terminal response. | Do not claim complete process lifecycle API or provider-covered process exit. |
| `src/tempus.fab` | Clock and sleep routes use `ad`; `vigila` is deferred because live inbound cursor returns from functions are not available. | Do not claim timer streams/cursor route support. |
| `src/crypta.fab` and `src/nuncius.fab` | Headers describe gateway-dispatched routes, but sampled functions remain `mori` deferrals. | Do not claim crypto or IPC provider support. |
| `docs/factory/nondum-mori-deferral-residual.md` | Runtime `mori` deferrals remain until free-function `@ nondum` support produces compile-time SEM017. | Public examples must not rely on deferred stubs without evidence. |

This supports a source-library planning claim, not a public runnable API claim.

## Host Gateway Evidence

The public host workspace has meaningful route infrastructure, but it is not
yet enough to unblock public product claims by itself:

| Repo | Evidence | Remaining public gate |
| --- | --- | --- |
| `host-kernel-rs` | README says it owns transport-neutral prefix routing and manifest validation, not OS effects or worker scheduling. Source validates manifest versions, prefixes, duplicate routes, and supported routes. | Needs export/run evidence tied to the released compiler and public contracts. |
| `host-native-rs` | README says it owns bounded worker scheduling and the `faber::HostDispatch` adapter, not Norma implementations. | Needs integration evidence for package bootstrap and released host dispatch. |
| `host-providers-rs` | Commit `fc08be5` records local manifest/dispatch coverage for `aleator` 5 routes, `consolum` 16, `processus` 10 provider-covered routes, `solum` 45, and `tempus` 4, with no manifest route missing from matching Rust dispatch strings. `processus:exi` is intentionally unmanifested and rejected until host exit has a protocol-visible terminal response. | Needs package/export linkage, public contract generation, and public example run evidence before support claims. |

Notable gaps relative to public copy:

- `providers.json` in the RC1 packet is still a placeholder.
- The current site does not publish a provider coverage matrix generated from
  exported manifests.
- Provider manifests do not cover deferred Norma modules such as `crypta` and
  `nuncius`.
- Some Norma functions require materializer/cursor work before a public API
  reference can mark them runnable.
- `processus:exi` is intentionally source-only/deferred rather than part of the
  local provider-covered process route count.
- This pass intentionally did not inspect or edit `radix` or `faber` while
  hand-1 owns active work there.

## Backlog Status

Recommended backlog state:

- **Now:** keep as P1 audit/status debt. No implementation required for the
  current RC1 private-preview packet.
- **Next P1 packets:** produce a provider route coverage matrix, export
  provider manifests into `providers.json`, and attach run evidence for selected
  Norma examples after the public source/export gate clears.
- **Promote to P0:** only when a release blocker or public route claims runnable
  Norma host-effect behavior, provider coverage, or host gateway support.

## Validation

This audit is docs/status only. No Norma source or host provider code changed.
The validation expectation for this artifact is:

```sh
git diff --check
./scripta/check-source
./scripta/audit-provider-route-claims
```
