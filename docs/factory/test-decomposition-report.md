# Test Decomposition Analysis — norma

## Summary

| Lens | Findings | Critical | High | Medium | Low |
|---|---|---|---|---|---|
| Coverage gaps | 22 | 5 | 5 | 8 | 4 |
| Missing negatives | 12 | 3 | 5 | 3 | 1 |
| Redundancy | 5 | 0 | 1 | 3 | 1 |
| Setup complexity | 3 | 0 | 1 | 2 | 0 |

**Total source modules:** 29 (25 public `.fab` files, 4 internal sub-modules)  
**Total exempla:** 20 (3 `proba`, 4 `chorda`, 1 `crypta-sha2`, 12 `stdlib-nativum`)  
**Inline probandum blocks:** 1 (in `model.fab`)  
**Modules with zero coverage:** 14

---

## Top 10 Recommendations (ranked by impact)

1. **[Critical] `src/solum.fab` → no meaningful exemplum** — 50+ public functions (lege, scribe, funde, hauri, enumera, dele, crea, amputa, exstat, explora, etc.). Only `solum-explora-contract` exists, and it is a one-line import smoke test with no assertions. The entire filesystem API surface is untested. This is the highest-risk gap in the stdlib.

2. **[Critical] `src/codex.fab` → no exemplum** — 9 public functions covering base64, hex, and URL encoding/decoding. All deferred (`mori`), but there are zero tests for the API contract (function signatures exist, tempta variants return `∪ nihil`, error-channel variants exist). When these bodies land, there is no guardrail.

3. **[Critical] `src/consolum.fab` → no exemplum** — 17 public functions for stdin/stdout/stderr. All `ad`-routed, but no exemplum exercises the route contracts, return types, or async/sync posture. Every command-line program depends on this module.

4. **[Critical] `src/crypta.fab` — only `digere` tested; negative gap** — The crypta-sha2 exemplum is thorough for digere (NIST vectors, invalid algorithm names), but: (a) the 64 MiB input-size guard (`iace` path) is never exercised; (b) `authenfica`, `cela`, `revela`, `signa`, `verifica`, `genera`, `gemina`, `derivabit` have no API contract tests at all (all deferred, but no test proves they throw the correct `mori` text). The deferred functions are security-critical.

5. **[High] `src/chorda.fab` — deferred functions untested** — `comprimata`, `angustata`, `temptat`, `discissa`, `pange`, `solve`, `tempta` are all deferred. The angustat exemplum exercises `temptat` and `comprimata` through their `mori` bodies, and discidit exercises `discissa`. But `pange`/`solve`/`tempta` (the UTF-8 mechanical trio) are only smoke-tested via `nota` output in `stdlib-nativum/chorda.fab` — not assertion-based. When these bodies replace `mori`, no negative tests guard invalid UTF-8 decode.

6. **[High] `src/chorda.fab` — `diducta` negative tests incomplete** — The diducta exemplum tests missing-opening-delimiter, but not: empty delimiter, document shorter than delimiter, missing closing delimiter, delimiter-only document, metadata with no trailing newline. The `iace` paths for several error conditions are untested.

7. **[High] `src/tempus.fab` — `structa` and `structa_momenti` error paths partially tested** — Only invalid day-of-month (Feb 30) is tested. Missing: month=0, month=13, hour=25, minute=60, second=61, year=0. The `⇥ textus` error channel has many untested arms.

8. **[High] `src/model.fab` probandum — safetensors binary edge cases missing** — The probandum tests negative shapes, decimals, and trailing separators but not: truncated header, zero-length data, invalid dtype strings, corrupt offset arrays, GGUF magic mismatch, GGUF version != 3, empty tensor names.

9. **[Medium] `src/valor.fab` — no dedicated exemplum** — 15 public functions (8 shape predicates, 5 accessors, 2 strict extractors). Tested only indirectly through `toml-navigatio` and `toml-exige-claves`. Missing: `listane` negative (non-lista), `carpe` out-of-bounds, `inveni` with non-numeric path segments on listae, `instansne` false-positive checks.

10. **[Medium] Redundancy: chorda functions tested in multiple exempla** — `retorta` is tested in both `retorta.fab` and `chorda.fab`. `retenta`/`expurgata` are tested in both `retine.fab` and `chorda.fab`. `discissa`/`residuum` in both `discidit.fab` and `chorda.fab`. The `stdlib-nativum/chorda.fab` exemplum is a catch-all that duplicates other exempla and only uses `nota` (output inspection), not `adfirma` (assertions). Consolidate or delete the catch-all.

---

## Per-Module Details

### `src/aleator.fab` → exempla: NONE
- **Coverage gaps:** All 5 public functions (`fractum`, `sortire`, `octetos`, `uuid`, `semina`) are `ad`-routed. Zero exempla exist to verify route contracts, return types, or edge cases (min=max for sortire, n=0 for octetos).
- **Severity:** High. Random number generation is foundational. An untested API surface is a bug risk for every consumer.
- **Recommendation:** Create at minimum: (1) verify `fractum()` returns float in [0,1); (2) `semina(42)` produces deterministic `sortire` output; (3) `uuid()` returns 36-char string; (4) `octetos(0)` returns empty bytes.

### `src/arca.fab` → exempla: NONE
- **Coverage gaps:** `connectet` + `Connexio` (9 methods) + `Transactio` (6 methods). All deferred via `mori`. No API contract tests exist.
- **Severity:** Low. All bodies are Stage 2 deferred. API surface is frozen in `implendum` blocks. When Stage 2 lands, the module needs exempla for: connection URL parsing, query parameter binding, transaction commit/rollback, error on closed connection.
- **Recommendation:** Add contract tests for `mori` error text once the concrete error messages are designed.

### `src/caelum.fab` → exempla: `exempla/caelum/*.proba` (compile-only)
- **Coverage gaps:** `ausculta`, `auscultabit`, `connecte`, `connectet` — all deferred. Sub-module exempla are compile-time type checks only (no runtime assertions).
- **Severity:** Low. All bodies are Stage 2 deferred.
- **Edge cases untested:** TLS cert/key validation, port range (0, 65535+), connection to unreachable host.

### `src/caelum/auscultator.fab` → exempla: `exempla/caelum/auscultator.proba`
- **Coverage gaps:** All methods deferred. Exemplum has 0 assertions — uses `nota` comments only. No actual `adfirma` calls.
- **Missing negatives:** None testable (all deferred).
- **Severity:** Low. Deferred, but exemplum should add at least type-shape assertions when bodies land.

### `src/caelum/connexus.fab` → exempla: `exempla/caelum/connexus.proba`
- **Coverage gaps:** All methods deferred. Exemplum has 0 assertions.
- **Severity:** Low. Same as auscultator.

### `src/caelum/terminus.fab` → exempla: `exempla/caelum/terminus.proba`
- **Coverage gaps:** Fully tested. Both construction and field access verified.
- **Missing negatives:** None. This is a pure data type.
- **Severity:** None. Good state.

### `src/chorda.fab` → exempla: `exempla/chorda/angustat.fab`, `exempla/chorda/diducta.fab`, `exempla/chorda/discidit.fab`, `exempla/chorda/retine.fab`, `exempla/stdlib-nativum/retorta.fab`, `exempla/stdlib-nativum/chorda.fab`
- **Coverage gaps:**
  - `retorta`: ✓ (tested in 2 exempla; one assertion-based, one output-based)
  - `nexa`: ✓ (tested in `stdlib-nativum/chorda.fab` via `nota` only — no `adfirma`)
  - `reputat`: ✓ (same — `nota` only)
  - `reciprocus`: ✓ (same)
  - `scala`: ✓ (tested in `retine.fab` with `adfirma`)
  - `retenta_iudicio`: ✓ (tested in `retine.fab` with `adfirma`)
  - `retenta`: ✓ (tested in `retine.fab` with `adfirma`)
  - `expurgata_iudicio`: ✓ (tested in `retine.fab` with `adfirma`)
  - `expurgata`: ✓ (tested in `retine.fab` with `adfirma`)
  - `residuum`: ✓ (tested in `discidit.fab` with `adfirma`)
  - `diducta`: PARTIAL (tested in own exemplum, but negative gaps)
  - `comprimata`: PARTIAL (exercised via `mori` in `stdlib-nativum/chorda.fab`; when body lands, needs whitespace edge cases)
  - `temptat`: ✓ (tested in `angustat.fab` with `adfirma` for both ASCII and non-ASCII)
  - `discissa`: PARTIAL (exercised via `mori`; happy path tested with `adfirma` in `discidit.fab`)
  - `pange`, `solve`, `tempta`: NOT TESTED with assertions. Only `nota` output inspection in `stdlib-nativum/chorda.fab`.
- **Missing negatives:**
  - `diducta`: empty delimiter, document shorter than delimiter, no closing delimiter, delimiter at document start with no metadata, delimiter-only document, metadata between empty fences.
  - `reputat`: empty haystack, empty needle, needle longer than haystack, overlapping pattern boundary. (Happy-path tested via `nota` only.)
  - `reciprocus`: empty string, single character, mixed case.
  - `retenta`/`expurgata`: both bounds `nihil`, string with no scalars in range, bounds reversed (infimum > supremum).
  - `scala`: empty string.
  - **Assertion counts:** `stdlib-nativum/chorda.fab` uses 0 `adfirma` calls — all `nota` output inspection.
- **Redundancy:** `retorta`, `retenta`, `expurgata`, `discissa`/`residuum` tested in both the dedicated exemplum and the catch-all `stdlib-nativum/chorda.fab`. The catch-all should be consolidated into per-function exempla with `adfirma` assertions.
- **Severity:** High. The UTF-8 mechanical trio (`pange`/`solve`/`tempta`) has no assertion-based tests despite being the trust-boundary between bytes and text. `diducta` has multiple untested error paths.

### `src/codex.fab` → exempla: NONE
- **Coverage gaps:** 9 public functions, all deferred. No API contract tests of any kind.
  - `base64`, `deBase64`, `temptaBase64`
  - `hex`, `deHex`, `temptaHex`
  - `url`, `deUrl`, `urlComponentum`, `deUrlComponentum`
- **Missing negatives:** When bodies land, every `tempta*` function needs invalid-input tests and every `de*` function needs malformed-input error-channel tests.
- **Severity:** Critical. Base64/hex are security-adjacent (auth tokens, API keys, hashes). URL encoding is correctness-critical for HTTP clients. Zero test coverage is a high-risk gap.

### `src/consolum.fab` → exempla: NONE
- **Coverage gaps:** 17 public functions, all `ad`-routed. Zero exempla.
  - `hauri`/`hauriet` (stdin bytes), `lege`/`leget` (stdin text)
  - `funde`/`fundet` (stdout bytes), `scribe`/`scribet` (stdout line), `dic`/`dicet` (partial output)
  - `mone`/`monet` (stderr), `vide`/`videbit` (debug)
  - `audit`, `loquitur`, `admonet` (TTY detection)
- **Severity:** Critical. Every application uses consolum. Untested I/O primitives are a correctness risk for the entire ecosystem.
- **Recommendation:** Create exemplum with `ad`-mocked route contracts or a dedicated consolum exemplum that exercises at least the sync text paths.

### `src/crypta.fab` → exempla: `exempla/crypta-sha2/src/main.fab`
- **Coverage gaps:** Only `digere` (SHA-2) is tested. The remaining 8 functions (`authenfica`, `cela`, `revela`, `signa`, `verifica`, `genera`, `gemina`, `derivabit`) are deferred and have zero test coverage.
- **digere tests:** 14 known-answer vectors across SHA-256 (4), SHA-384 (4), SHA-512 (6) covering: empty input, "abc", embedded-zero, padding-boundary (55/111 bytes), multi-block (65/129 bytes). ✓
- **digere negative tests:** 5 invalid algorithm names tested (empty, "md5", "SHA256", "Sha384", "sha-256"). ✓
- **Missing negatives (digere):**
  - Input exactly at 64 MiB limit (67108864 bytes) — boundary test.
  - Input exceeding 64 MiB limit — `iace` path for "input exceeds 67108864-byte limit".
  - These are the only untested `iace` paths in a live native-Faber body. **Critical gap.**
- **Missing negatives (deferred functions):** No exemplum tests that `authenfica("sha256", ...)`, `cela("aes-256-gcm", ...)`, etc. throw `mori` with the expected error text. When bodies land, each deferred function needs:
  - Invalid algorithm name tests
  - Key size validation (AES-256 requires 32-byte keys)
  - IV/ciphertext length validation for `revela`
  - Signature verification with wrong key for `verifica`
- **Assertion counts:** crypta-sha2 exemplum has: 14 happy-path assertions (known-answer vectors) + 5 error-path assertions (invalid algorithms) = 19 total (14:5 ratio). Strong positive-to-negative ratio for what's covered.
- **Severity:** Critical (for the 64 MiB boundary gap). High (for the 8 deferred functions with no contract tests). Cryptography is the highest-stakes module in the stdlib.

### `src/csv.fab` → exempla: `exempla/stdlib-nativum/csv-chorda.fab`
- **Coverage gaps:** `solve` and `pange` both depend on `chorda.discissa` (deferred). The exemplum exercises them but via `mori` paths. The exemplum uses `nota` output inspection, not `adfirma` assertions.
- **Missing negatives:**
  - Empty wire → `solve` returns `vacua` ✓ (code has this guard)
  - Single-row, no header distinction
  - Fields with embedded commas (not handled — known limitation per csv-methods.md)
  - CRLF row endings (handled by `_normaliza_row` but not tested)
- **Assertion counts:** 0 `adfirma` calls. 2 `nota` calls for output inspection.
- **Severity:** Medium. Deferred dependency blocks real testing, but when `discissa` ships, csv needs assertion-based exempla.

### `src/fila.fab` → exempla: NONE
- **Coverage gaps:** `Fila<T>` genus (10 methods) + 2 constructors. TARGET form — will not compile today.
- **Severity:** Low. Module is blocked on compiler work. But the design is committed; when the compiler lands, exempla are needed for: empty-queue pop, single-element push/pop cycle, rebalance edge cases (many head-pushes then tail-pops, and vice versa), `collecta` ordering, `primus`/`ultimus` on empty queue.

### `src/http.fab` → exempla: NONE
- **Coverage gaps:** 6 client methods (`petet`, `mittet`, `ponet`, `delet`, `mutabit`, `rogabit`) + 4 server methods (`listen`, `accept`, `respond`, `stop`) + `Replicatio` implendum (7 methods) + `Rogatio` implendum (6 methods). All deferred or `ad`-routed.
- **Severity:** Medium. HTTP is the primary application protocol. Deferred is understandable for Stage 1, but server-side listen/accept/respond should have route-contract exempla.

### `src/json.fab` + `src/json/*.fab` → exempla: NONE
- **Coverage gaps:** `json.pange`, `json.solve`, `json.tempta` are facades over compiler conversio. The sub-modules (`cursor.fab`, `lexica.fab`, `pange.fab`, `solve.fab`) are internal. No dedicated json exemplum exists.
- **Missing negatives (when bodies land):**
  - `solve`: malformed JSON (unterminated string, trailing comma, bare keywords without quotes, unicode escape edge cases)
  - `tempta`: verify returns `nihil` on all the above
  - `pange`: verify round-trip identity
- **Severity:** Medium. JSON is the universal wire format. The lexica module has detailed internal helpers (`_est_digita`, `_transeant_liber`, `_destruat_liber`, `_valor_hexadecimalis`) that are currently untested because they are `@ privata` and no public exemplum exercises them through `solve`/`pange`.

### `src/mathesis.fab` → exempla: `exempla/stdlib-nativum/mathesis.fab`, `exempla/stdlib-nativum/mathesis-operators.fab`
- **Coverage gaps:** 28 public functions. Most are tested.
  - Constants: ✓ (PI, E, TAU — tested in `mathesis.fab`)
  - Clamp: ✓ (`constringens` tested)
  - Arithmetic catalog: ✓ (`addita`, `subtracta`, `multiplicata`, `divisa`, `modulata`, `negativa` — tested in `mathesis-operators.fab`)
  - Ordering catalog: PARTIAL (`minime` tested; `minor`, `maior`, `maxime` NOT tested)
  - Bitwise catalog: PARTIAL (`coniuncta`, `disiuncta`, `alternata`, `complementata` NOT tested)
  - Shift catalog: ✓ (`sinistrata`, `dextrata` tested)
  - Transcendentals: ✓ (all 8 tested)
- **Missing negatives:**
  - `divisa(a, 0.0)` — division by zero
  - `constringens` with lo > hi, lo = hi
  - `modulata(a, 0.0)` — modulo by zero
  - `sinistrata`/`dextrata` with shift >= 64
  - `potentia(0.0, 0.0)` — 0^0
  - Transcendental worst-case inputs: `exponens(100.0)` (overflow), `sinus(1e15)` (precision loss)
- **Redundancy:** `mathesis-operators.fab` tests arithmetic and shift catalog functions that are also receiver methods on `fractus`/`numerus`. The mathesis catalog and receiver methods are distinct surfaces that should both be tested, but the exemplum is a `nota`-based output-check, not `adfirma`-based.
- **Assertion counts:** `mathesis.fab`: 20 assertions (13 `nota` output checks, 7 `adfirma`). `mathesis-operators.fab`: 5 `adfirma` + 5 `nota`. Good ratio overall.
- **Severity:** Medium. Good coverage for transcendentals; missing edge cases for division by zero and overflow.

### `src/model.fab` → exempla: NONE (inline `probandum` block in source)
- **Coverage gaps:** 11 public functions + 3 genera. Inline probandum covers:
  - `formatum`: ✓ (3 formats tested: missing-download → alias-missing, .gguf suffix, .safetensors suffix)
  - `vacua_summarium`: ✓
  - `inanis_diagnosticum`: ✓
  - `severitas_pro_status`: ✓
  - `safetensors_header_textus`: ✓ (implicitly via `safetensors_summarium`)
  - `safetensors_summarium`: ✓ (2 happy-path: normal shape, shape with spaces)
  - `tempta_safetensors_summarium`: ✓ (3 error-path: negative dim, decimal dim, trailing separator)
  - `gguf_summarium`: ✓ (1 happy-path: toy GGUF with architecture, name, file_type, quantization_version)
  - `gguf_quantization_fragmentum`: NOT tested
  - `tempta_gguf_summarium`: NOT tested
  - `diagnosticum`: PARTIAL (tested only via `inanis_diagnosticum`)
- **Missing negatives:**
  - `safetensors_header_textus`: data < 8 bytes, header_len > remaining data, non-ASCII header byte
  - `safetensors_summarium`: empty tensor name, unknown dtype, shape with zero dimension, missing "dtype" field, missing "shape" field, corrupted JSON
  - `gguf_summarium`: magic mismatch, version != 3, truncated header (< 24 bytes), zero tensor count, empty metadata
  - `formatum`: "router-backed" status doesn't test the "missing-download" branch — the source contains it but the test only covers "missing-download" and file-suffix branches
- **Assertion counts:** 10 probae across 1 probandum. Happy-path assertions: ~18. Error-path assertions: ~6. Ratio: 3:1. Good but could improve on binary edge cases.
- **Severity:** High. The model metadata parser is the most complex native Faber code in the repo (GGUF is ~350 lines of binary parsing). Untested error paths in binary format parsing are bug risks.

### `src/nuncius.fab` → exempla: NONE
- **Coverage gaps:** 5 public functions + 5 implendums (ParPortarum, Porta, Mutex, Semaphorum, Conditio). All deferred.
- **Severity:** Low. All Stage 2 deferred.

### `src/ordinata.fab` → exempla: NONE
- **Coverage gaps:** `Ordinata<K,V>` genus (10 methods) + 2 constructors. TARGET form — will not compile.
- **Severity:** Low. Blocked on compiler work. Exempla needed for: insert order preservation, range query edge cases (inferior > superior, empty range), delete-then-reinsert ordering, `fines` with bounds outside key set.

### `src/pressura.fab` → exempla: NONE
- **Coverage gaps:** 4 public functions + 2 implendums (Compressor, Decompressor). All deferred.
- **Severity:** Low. All Stage 2 deferred.

### `src/processus.fab` → exempla: NONE
- **Coverage gaps:** 12 public functions. `genera` is deferred; `dimitte`, `captura`, `exsequi`, `exsequetur`, `lege`, `scribe`, `sedes`, `muta`, `identitas`, `argumenta`, `exi` are `ad`-routed.
- **Severity:** Medium. Process management is security-adjacent (shell execution, environment variables, CWD manipulation). `captura` and `legere` (env read) have no exempla.

### `src/solum.fab` → exempla: `exempla/stdlib-nativum/solum-explora-contract.fab`
- **Coverage gaps:** 50+ public functions. Only `explora` has an exemplum, and it is a 1-line import smoke test (prints "explora-contract"). Zero `adfirma` assertions.
  - Not tested: `lege`/`leget`, `hauri`/`hauriet`, `partem`, `inveni`, `carpe`/`carpiet`, `scribe`/`scribet`, `funde`/`fundet`, `appone`/`apponet`, `exstat`/`exstabit`, `directoriumne`, `regularene`, `legibilene`, `vinculumne`, `mensura`, `modus`, `describe`/`describet`, `sequere`/`sequetur`, `vincula`, `dele`/`delet`, `exscribe`/`exscribet`, `renomina`/`renominabit`, `tange`/`tanget`, `modum`, `crea`/`creabit`, `enumera`/`enumerabit`, `amputa`/`amputabit`, path proxy functions (`iunge`, `parens`, `nomen`, `suffixum`, `absolve`, `domus`, `temporarium`), `explora`.
  - `explora` is native Faber code (50+ lines of glob logic) with zero runtime test.
- **Missing negatives (explora):**
  - Empty radix, empty exemplar, exemplar with empty segment ("//"), exemplar with no match, exemplar matching no files, radix that doesn't exist (`exstat` returns falsum, but does `enumera` throw?)
  - The `iace "empty path segment in solum.explora exemplar"` path is untested.
- **Severity:** Critical. The filesystem module is the most-used surface in the stdlib after consolum. 50+ untested functions, including file I/O, directory operations, and glob. `explora` is live native code with untested error paths.

### `src/solum/path.fab` → exempla: NONE
- **Coverage gaps:** 7 public functions, all `ad`-routed. Zero exempla.
- **Severity:** Medium. Path operations are correctness-critical (import resolution, file I/O). At minimum: `iunge`(["a", "b", "c"]) ≡ "a/b/c", `parens`("/a/b") ≡ "/a", `nomen`("/a/b.txt") ≡ "b.txt", `suffixum`("file.tar.gz") ≡ ".gz".

### `src/tempus.fab` → exempla: `exempla/stdlib-nativum/tempus-civil.fab`
- **Coverage gaps:** 20 public functions.
  - wire/civil: ✓ (`solve`, `exprimit`, `annus`, `mensis`, `dies`, `hora`, `minuta`, `secunda`, `structa`, `structa_momenti`)
  - clock: NOT TESTED (`nunc`, `monotonicum`, `activum` — `ad`-routed)
  - sleep/timer: NOT TESTED (`dormiet`, `expectet`, `vigila` — `ad`-routed or deferred)
  - duration constants: NOT TESTED (`MILLISECUNDUM`, `SECUNDUM`, `MINUTUM`, `HORA`, `DIES`)
- **Missing negatives:**
  - `solve` with invalid wire format (non-RFC3339 string)
  - `structa(2026, 13, 1)` — invalid month (only Feb 30 tested; what about month=0, month=13?)
  - `structa_momenti(2026, 1, 1, 25, 0, 0)` — invalid hour
  - `structa_momenti(2026, 1, 1, 0, 60, 0)` — invalid minute/second
  - `structa(2026, 0, 1)` — month=0
  - `structa(0, 1, 1)` — year=0 (valid in astronomical convention, but is it accepted?)
- **Assertion counts:** 12 `adfirma` assertions: 10 happy-path, 2 error-path (`expectInvalid`). Good structure with `build()`/`buildMoment()` error-wrapping helpers. But `expectInvalid` only tests one invalid date.
- **Severity:** High. Date/time is correctness-critical. Untested invalid-input paths on `structa`/`structa_momenti` are bug risks. Clock and duration constants need trivial tests.

### `src/tensor.fab` → exempla: `exempla/stdlib-nativum/tensor-bridge.fab`, `exempla/stdlib-nativum/tensor-applicata.fab`
- **Coverage gaps:** 6 public functions.
  - `planata`: ✓ (tested in tensor-bridge)
  - `structa`: ✓ (tested in both exempla)
  - `productum`: ✓ (tested in tensor-bridge)
  - `applicata`: ✓ (tested in tensor-applicata)
  - `summa`: ✓ (tested in tensor-applicata)
  - `summa_fractus`: NOT TESTED
- **Missing negatives:**
  - `structa` with empty lista
  - `applicata` with identity function, constant function
  - `summa` on empty tensor
  - `productum` with empty shape → should be 1 (guard tested?)
- **Assertion counts:** tensor-bridge: 3 `nota` (output inspection). tensor-applicata: 1 `nota`. 0 `adfirma` calls across both exempla. **This is a problem** — no assertions verify correctness; tests rely on manual output inspection.
- **Severity:** Medium. Missing assertion-based tests and `summa_fractus` coverage.

### `src/thesaurus.fab` → exempla: NONE
- **Coverage gaps:** 11 public functions + implendums (Subscriptio, Nuntius). All deferred.
- **Severity:** Low. All Stage 2 deferred.

### `src/toml.fab` → exempla: `exempla/stdlib-nativum/toml-navigatio.fab`, `exempla/stdlib-nativum/toml-exige-claves.fab`
- **Coverage gaps:** `pange`, `solve`, `tempta`. All deferred. Exempla test the downstream `valor.*` navigation surface, not the TOML wire floor itself.
- **Missing negatives:** When TOML bodies land:
  - `solve`: malformed TOML (unterminated string, invalid date, duplicate key, mixed array types)
  - `tempta`: verify returns `nihil` on parse errors
  - `pange`: verify round-trip identity with known TOML documents
- **Severity:** Low for API contract (deferred). Medium for when bodies land.

### `src/valor.fab` → exempla: NONE (tested indirectly via toml exempla)
- **Coverage gaps:** 15 public functions. Tested indirectly:
  - `nihilne`: ✓ (tested via `toml-navigatio`: `adfirma valor.nihilne(doc) ≡ falsum`)
  - `bivalensne`: NOT TESTED
  - `textusne`: ✓ (tested via `toml-navigatio`)
  - `numerusne`: ✓ (tested via `toml-navigatio`)
  - `fractusne`: NOT TESTED
  - `listane`: NOT TESTED
  - `tabulane`: ✓ (tested via `toml-navigatio`)
  - `instansne`: NOT TESTED
  - `cape`: ✓ (tested via both toml exempla)
  - `carpe`: NOT TESTED
  - `inveni`: ✓ (tested via `toml-navigatio`: dotted path `"id"`)
  - `inveni_partem`: NOT TESTED directly (tested via `inveni`)
  - `exige`: ✓ (tested via both toml exempla)
  - `exige_claves`: ✓ (tested via `toml-exige-claves`)
- **Missing negatives:**
  - `listane(valor(42))` — false positive check
  - `carpe(v, 0)` on non-lista valor
  - `carpe(v, 999)` — out of bounds on lista
  - `inveni(v, "a.b")` where "a" is a lista — exercises `inveni_partem` numeric-index fallback
  - `inveni(v, "a.b")` where "a" is a textus — exercises non-collection `redde nihil` branch
  - `exige` with non-textus value at key
  - `exige_claves` with partial match (first key exists, second doesn't)
  - All shape predicates with wrong-type inputs (e.g., `textusne` on a numerus valor)
- **Severity:** Medium. Tested through TOML but missing dedicated exemplum for shape predicates and lista-based accessors. `carpe` and `inveni` numeric-index paths are untested.

### `src/vector.fab` → exempla: `exempla/stdlib-nativum/vector-pending-placeholder.fab`
- **Coverage gaps:** 7 public functions. Exemplum is an expected-compile-failure placeholder.
- **Severity:** Low. Pending implementation. Placeholder correctly blocks runtime use.

### `src/yaml.fab` → exempla: NONE
- **Coverage gaps:** 5 public functions, all deferred.
- **Severity:** Low. All Stage 2 deferred.

---

## Redundancy Detail

### Cross-exemplum overlaps

| Function | Tested in |
|---|---|
| `chorda.retorta` | `exempla/stdlib-nativum/retorta.fab` (dedicated) + `exempla/stdlib-nativum/chorda.fab` (catch-all) |
| `chorda.nexa` | `exempla/stdlib-nativum/chorda.fab` only |
| `chorda.reputat` | `exempla/stdlib-nativum/chorda.fab` only |
| `chorda.reciprocus` | `exempla/stdlib-nativum/chorda.fab` only |
| `chorda.retenta` | `exempla/chorda/retine.fab` (dedicated) + `exempla/stdlib-nativum/chorda.fab` (catch-all) |
| `chorda.expurgata` | `exempla/chorda/retine.fab` (dedicated) |
| `chorda.scala` | `exempla/chorda/retine.fab` (dedicated) |
| `chorda.discissa` | `exempla/chorda/discidit.fab` (dedicated) + `exempla/stdlib-nativum/chorda.fab` (catch-all) |
| `chorda.comprimata` | `exempla/chorda/angustat.fab` (dedicated) + `exempla/stdlib-nativum/chorda.fab` (catch-all) |
| `mathesis.PI`/etc. | `exempla/stdlib-nativum/mathesis.fab` (dedicated) + `exempla/stdlib-nativum/mathesis-operators.fab` (operators) |

The catch-all `stdlib-nativum/chorda.fab` exemplum is the primary redundancy source. It uses 0 `adfirma` assertions — only `nota` calls for manual output inspection. It duplicates tests from 4 other exempla without adding value. **Recommendation: delete it** and ensure the dedicated exempla have full `adfirma` coverage.

### Copy-pasted setup blocks

- `sampleToml()` is copy-pasted identically between `toml-navigatio.fab` and `toml-exige-claves.fab`.
- `chorda` import line (`importa ex "norma:chorda" privata chorda`) appears in 7 exempla.
- `incipit { ... }` boilerplate appears in every exemplum.

The `sampleToml()` duplication is the only actionable redundancy. **Recommendation:** extract into a shared `exempla/communia/toml-fixtures.fab` if the toolchain supports it.

---

## Setup Complexity Analysis

### Exempla with highest setup-to-assertion ratio

| Exemplum | Setup lines | Assertion lines | Ratio | Notes |
|---|---|---|---|---|
| `crypta-sha2/src/main.fab` | 31 (helpers: `expect`, `_repete`) | 25 (vectors + error tests) | 1.24:1 | Acceptable — crypto helpers are necessary |
| `model.fab` probandum | 0 (inline in source) | 60+ (across 10 probae) | 0:1 | Inline tests have zero import/setup overhead |
| `chorda/diducta.fab` | 23 (sampleSkill, newline helpers) | 9 | 2.5:1 | `sampleSkill()` string concat is verbose but necessary |
| `tempus-civil.fab` | 24 (epochZero, build, buildMoment, expectInvalid) | 18 | 1.3:1 | Error-wrapping helpers suggest the API could expose a try-structa variant |
| `toml-navigatio.fab` | 11 (sampleToml, imports) | 13 | 0.85:1 | Good ratio |
| `stdlib-nativum/retorta.fab` | 3 (import, incipit) | 2 | 1.5:1 | Minimal |
| `stdlib-nativum/solum-explora-contract.fab` | 2 | 1 | 2:1 | Smoke test — no real setup |

**No disproportionate setup-to-assertion ratios found.** The highest ratio (diducta at 2.5:1) is still reasonable for a document-processing function where constructing realistic test input requires multiple lines.

### Decomposition signals

- **`tempus.fab`**: The `build()`/`buildMoment()` error-wrapping helpers in the exemplum suggest callers want a `tempta_structa()` variant that returns `instans ∪ nihil` rather than throwing. This is a design signal, not a test problem. Currently, callers must wrap `structa` in `fac`/`cape` blocks for every construction — the exemplum's verbose wrappers prove this ergonomic cost.

- **`chorda.fab`**: `diducta` tests only two scenarios in 43 lines. A test table over `[{doc, delim, expected_meta, expected_corpus}]` would be more efficient but Faber's exemplum format doesn't support data-driven test tables.

- **`model.fab` probandum**: The inline probandum is well-structured with clear proba names. The safetensors and GGUF hex literals are necessarily verbose. No decomposition signal — binary format testing requires binary fixtures.

---

## Methodology Notes

- **Source inventory:** 25 public `.fab` files + 4 internal sub-modules under `src/`.
- **Exempla inventory:** 20 files across 3 directories (`exempla/caelum/`, `exempla/chorda/`, `exempla/crypta-sha2/`, `exempla/stdlib-nativum/`).
- **Validation:** `./scripta/check-source` was not run in this analysis (read-only scope). The report is based on static code inspection.
- **Assertion counting:** `adfirma` and `proba` calls counted as assertions. `nota` calls (output inspection) counted separately and noted as weaker than `adfirma` for automated verification.
- **Deferred functions:** Functions with `mori` bodies are counted as "deferred pending Stage 2 dispatch." Their API contracts (function signatures, return types) are checked for exempla coverage, but negative testing of error texts is flagged as lower priority.
- **TARGET modules:** `fila.fab` and `ordinata.fab` are marked TARGET — they will not compile. Exempla gaps are noted but ranked Low.
