# Residual chart: norma deferred stubs — `mori` vs `@ nondum` (SEM017)

**Status:** exact residual inventory (2026-07-14) — **not** bulk-migrated
**Source:** `./scripta/audit-deferred-mori`
**Owner:** hunter-1 (norma main spine); radix free-function SEM017 is a **hard gate**

## Problem

88 Norma public stubs use runtime `mori "norma:... deferred pending Stage N"`
bodies. Callers **typecheck green** and only fail when executed.

The compiler already owns a stronger hub:

| Mechanism | When | Fail |
| --- | --- | --- |
| `@ nondum [TARGET] ["REASON"]` | call site (today: **interface methods**) | **SEM017** compile-time |
| `mori "…"` leaf body | runtime | process abort |

Using `mori` for pure deferral hides unavailability until run — against
fail-closed / truth camp norms.

## Inventory (repeatable)

Run from the Norma repo root:

```bash
./scripta/audit-deferred-mori
```

The command scans `src/**/*.fab` for
`mori "norma:... deferred pending Stage ..."` and currently reports **88**
matching stubs across **16** source files. All current matches are **Stage 2**.

Current grouping:

| Source file | Count |
| --- | ---: |
| `src/arca.fab` | 1 |
| `src/caelum.fab` | 4 |
| `src/caelum/auscultator.fab` | 4 |
| `src/caelum/connexus.fab` | 7 |
| `src/chorda.fab` | 7 |
| `src/codex.fab` | 10 |
| `src/crypta.fab` | 9 |
| `src/http.fab` | 12 |
| `src/nuncius.fab` | 5 |
| `src/pressura.fab` | 4 |
| `src/processus.fab` | 1 |
| `src/solum.fab` | 3 |
| `src/tempus.fab` | 1 |
| `src/thesaurus.fab` | 12 |
| `src/toml.fab` | 3 |
| `src/yaml.fab` | 5 |

Any future stub with real logic must **not** be auto-converted merely because it
contains a `mori` expression.

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

Unit A green + Unit B pilot green + the `./scripta/audit-deferred-mori`
inventory count ratchets toward zero under Unit C (or explicit counted debt
budget).
