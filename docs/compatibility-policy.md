# Norma Compatibility Policy

**Version**: `compatibility-policy v1.0.0` (2026-08-16, english-locale closeout)
**Repo**: norma. **Applies to**: the norma standard-library source package
(`cista.toml` version 0.1.0, pre-1.0) and its public reader surface
(`norma:*` import coordinates and member names).
**Row-level authority**: each locale-pack `[[library_members]]` row in
`radix/stdlib/locale/{en,la}/pack.toml` is the row-level surface mapping;
this document is the product-facing aggregate that names what breaks, what
migrates, and the identity rules.

## 1. Pre-1.0 clean-break posture

Norma is **pre-1.0** (`cista.toml` version 0.1.0). There is **no stability
promise before 1.0**. Breaking changes are allowed and executed as **clean
breaks** — no forwarding shims, no containment facades, no deprecation window —
consistent with the faber product clean-break posture.

Every break is **recorded**: the commit message + this policy carry the note
(and, where a goal owns the change, the goal closeout). Consumers migrate at
the new shape.

## 2. The english-locale clean break (2026-08-15/16)

The [`english-locale` goal](factory/english-locale/goal.md) converted norma
from the Latin reader surface to English in two passes (Pass A: keyword/type/
intrinsic surface + locale; Pass B: user identifiers). This is the canonical
pre-1.0 clean break, recorded here.

### What broke

| Surface | Before | After |
| --- | --- | --- |
| Reader locale | `la` (Latin keywords/types) | `en` canonical surface |
| User identifiers | Latin member names (`chorda.retorta`, `solum.lege`, `valor.cape`, …) | English member names (`reverse`, `read_file`, `get`, …) |
| Source aliases | — | **none** — no forwarding shims, no `fn` aliases in `src/` |

The full rename ledger per module is in
[`factory/english-locale/pass-b-delivery.md`](factory/english-locale/pass-b-delivery.md)
§7. Pass B renamed **members** only; retained Latin **params** are sanctioned
by the delivery policy (§12), not a compatibility surface.

### What stays

- **`norma:*` import coordinates and module aliases are unchanged.**
  `norma:chorda`, `norma:solum`, … resolve as before; only member spellings
  moved.
- **La readers are supported via la-pack rows.** `radix/stdlib/locale/la/
  pack.toml` carries **87** `package = "norma"` `[[library_members]]` rows
  mapping the new English canonicals back to the Latin surface
  (`reverse→retorta`, `join→nexa`, `read_file→lege`, `get→cape`, …), so a
  Latin-locale consumer compiling `chorda.retorta(...)` / `solum.lege(...)`
  resolves. The en pack's `norma:*` rows are the canonical identity surface
  (9 rows).
- **No source aliases.** The la compatibility is delivered by the locale
  packs, never by source-level alias functions in `src/`. A grep for the
  retired Latin member names in `src/**/*.fab` code tokens is clean
  (`scripta/check-source` guards the ledger names).

## 3. Identity rules

- **Import coordinate stability.** `norma:*` coordinates are the stable
  identity of a module; member names are not. The clean break moved members,
  never coordinates.
- **Locale-pack mapping is the translation layer.** The la pack row set (87
  rows) is the complete translation of the renamed public surface. A row
  missing from the la pack means an la reader of that member reads the
  English canonical — the invariant is "a Latin reader consuming norma loses
  nothing" via pack rows, not via source aliases.

## 4. Versioning

This policy is versioned (`compatibility-policy v1.0.0`) per the schema-version
convention. Pre-1.0 clean breaks may change this policy; each change records a
new patch/minor/major stamp per the convention. The locale-pack row sets are
the row-level authority on any disagreement with this aggregate.
