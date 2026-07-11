# Residual chart: norma deferred stubs — `mori` vs `@ nondum` (SEM017)

**Status:** charted residual (2026-07-11) — **not** bulk-migrated  
**Source:** purity `3b178bd` / hunter-1 task `7ed426b`  
**Owner:** hunter-1 (norma main spine); radix free-function SEM017 is a **hard gate**

## Problem

~90 norma public stubs use runtime `mori "norma:… deferred pending Stage N"`
bodies. Callers **typecheck green** and only fail when executed.

The compiler already owns a stronger hub:

| Mechanism | When | Fail |
| --- | --- | --- |
| `@ nondum [TARGET] ["REASON"]` | call site (today: **interface methods**) | **SEM017** compile-time |
| `mori "…"` leaf body | runtime | process abort |

Using `mori` for pure deferral hides unavailability until run — against
fail-closed / truth camp norms.

## Inventory (grep, approximate)

Pattern: `mori "norma:` under `norma/src/**/*.fab` — on the order of **~90**
sites across **~17** packages (thesaurus, http, codex, crypta, chorda, caelum,
yaml, nuncius, pressura, toml, …). Sampled pure-deferral bodies (single `mori`
line). Any stub with real logic must **not** be auto-converted.

## Preferred end state

1. Pure deferral free functions carry:
   ```text
   @ nondum "deferred pending Stage N dispatch"
   functio capiet(...) → T { }
   ```
2. Call sites get **SEM017** with REASON (import/signature still resolve).
3. No hand-authored per-fn abort strings for pure stubs.

## Blocker (why pilot is not a silent `@ nondum` swap)

| Fact | Evidence |
| --- | --- |
| SEM017 on **methods** | `typecheck/call.rs` `report_nondum_method_call` + interface method `availability` |
| Free **functions** | `HirFunction` has **no** `availability` field; `check_call` Path path does not consult `@ nondum` |
| Annotation lower | interface methods get `lower_availability_from_annotations`; free funcs keep annotations but do not gate calls |

So converting norma free functions to `@ nondum` **today** would drop the
runtime abort **without** gaining compile-time SEM017 — **worse** (silent no-op
or empty body).

## Ordered work (next kills)

| Unit | Scope | Owner |
| --- | --- | --- |
| **A. Free-function nondum hub** | `HirFunction.availability`; lower from `@ nondum`; `check_call` SEM017; driver tests (no-target + target-tagged) | hunter-1 radix |
| **B. Pilot package** | After A: convert `norma/src/toml.fab` (3 pure stubs) + one exemplum that expects SEM017 | hunter-1 norma |
| **C. Mechanical pass** | Script remaining pure `mori "norma:… deferred…"` → `@ nondum` + empty body; audit partial-impl exceptions; re-green exempla | hunter-1 or short norma packet |

## Out of scope this chart

- Full 90-file rewrite in one kill  
- Partial-impl functions that use `mori` as a branch (keep body)  
- Changing Stage numbering / package façades  

## Done-when for residual close

Unit A green + Unit B pilot green + inventory count of pure `mori` deferrals
ratchets toward zero under Unit C (or explicit counted debt budget).
