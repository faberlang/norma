+++
locale = "en"
+++

# Design: pro-level HTTP module (`norma:http`, directory form)

**Status**: active — H1 directory form landed (hand 3db5a792); H2–H6 remain

**Driver**: Inferentia — token streaming via SSE over a loopback HTTP/1.1
server is the load-bearing case. Non-goals: replacing the hosts provider with
in-language sockets, WebSockets, HTTP/2 or /3 (see §7).

Evidence is live code at HEAD of the sibling checkouts (2026-08-18). Every
claim below carries `file:line`. Confidence tags: **known** (cited code),
**inferred** (reasoned from cited code, not executed), **unverified** (stated
by a doc/comment, not proven here).

---

## 1. What exists today

### 1.1 Norma surface — `norma/src/http.fab` (126 lines, 10 functions, 2 interfaces)

| Item | State | Evidence |
| --- | --- | --- |
| `get` | `@ public @ future`, `mori` panic stub ("deferred pending Stage 2 dispatch") | `norma/src/http.fab:25-29` |
| `post`, `put`, `delete`, `patch`, `send_request` | `@ future` only (no `@ public` — annotation drift), panic stubs | `norma/src/http.fab:32-66` |
| `listen`/`accept`/`respond`/`stop` | plain `call 'http:*'` wrappers, **no annotations at all** (drift; currently still exported for package paths per `radix/crates/radix-program/src/library.rs:1600-1606` — unmarked package-path declarations stay exported until the radix-scoped module-private flip) | `norma/src/http.fab:70-91` |
| `interface Response`, `interface Request` | declared; nothing constructs or implements them | `norma/src/http.fab:94-127` |

### 1.2 Hosts provider — `hosts/crates/http` (server `src/lib.rs` 643 lines + client `src/client.rs`)

Server (`src/lib.rs`):

- Exactly four routes: `http:listen|accept|respond|stop` (`src/lib.rs:114-123`,
  `src/manifest.json:7-10`).
- **Loopback-only**: binds `("127.0.0.1", port)` hardcoded (`src/lib.rs:142`).
- **One request per connection**: `respond` writes then
  `shutdown(Shutdown::Both)` (`src/lib.rs:236-240`); the response head
  hardcodes `connection: close` (`src/lib.rs:509-513`).
- **No streaming**: `format_response` builds one `Vec<u8>` (status line +
  provider-owned `connection`/`content-length` + body) and writes it once
  (`src/lib.rs:509-524`). `transfer-encoding` requests are rejected
  (`src/lib.rs:421-425`) — no chunked, in either direction.
- **Blocking pull + 5 ms sleep poll**: `POLL_INTERVAL = 5 ms`
  (`src/lib.rs:27`); `accept` and both header/body reads poll
  (`src/lib.rs:163-203`, `292-366`).
- Caps: 1 MiB default / 16 MiB max body, 64 KiB headers, 250 ms write
  timeout (`src/lib.rs:24-28`).
- Provider-owned hop headers: caller `connection` and `content-length` are
  rejected (`src/lib.rs:496-500`); request id echoed as
  `x-faber-request-id` (`src/lib.rs:512`).
- Request carrier: valor table `{id, method, path, headers: [{name,value}], body: octeti}`
  (`src/lib.rs:450-458`).
- Single global pending-request map (`src/lib.rs:37-39`, `48`) — one
  in-flight request per process, not per connection.

Client (`src/client.rs`):

- `petet/mittet/ponet/delet/mutabit/rogabit` + `Replicatio` carrier, over
  blocking `ureq` 2 (`src/client.rs:68-108`, `hosts/crates/http/Cargo.toml:14`).
- **New `ureq::Agent` per request** (`src/client.rs:110-112`) — no connection
  reuse across calls from generated code.
- **Whole-body buffering**: `read_to_end` (`src/client.rs:137-142`) — no
  streaming receive, no incremental SSE consumption.
- `async fn` wrappers around blocking calls (`src/client.rs:68-91`) — the
  async posture is nominal, execution blocks.
- Transport errors surface as a synthetic status-599 response
  (`src/client.rs:93-99`); 30 s timeout (`src/client.rs:111`).
- TLS: `ureq = "2"` with default features (rustls) — the **client** can speak
  https today (**inferred** from the default feature set; not exercised here).

### 1.3 Compiler special-case — drifted and dangling

`radix/crates/radix-program/src/library.rs` special-cases `norma:http`
**only in single-segment form** (`is_builtin_norma_http_module`,
`library.rs:1556-1558`) and maps client items to a runtime path
(`norma_http_runtime_path`, `library.rs:1564-1573`):

- It matches the **Latin** verb names `petet|mittet|ponet|delet|mutabit|rogabit`
  and interface `Replicatio` — none of which exist in the live
  `norma/src/http.fab` (which has `get/post/put/delete/patch/send_request`,
  `Response`, `Request`). The mapping can never fire against the live module
  (**known**: name sets are disjoint).
- Even if it fired, its target `faber::http::*` does not exist:
  `faber/runtime/rust/src/lib.rs:7-22` has no `http` module (**known**).
- Interfaces are elided from generated Rust only for `norma:http`
  (`library_item_elides_generated_rust_decl`, `library.rs:1549-1553`).

This is dead, drifted special-casing from an earlier http.fab naming. The live
green test (`radix/crates/faber/src/package_test.rs:5434-5506`) uses
`http.get(...)` and asserts the **generic** generated path `crate::http::get`
— it does not exercise the special-case.

### 1.4 Consumers of `norma:http`

- **inferentia** — the sole Faber consumer, and it does **not import the
  module**: it re-declares local route wrappers `http_listen/http_accept/
  http_respond_raw/http_respond` calling the same provider routes
  (`inferentia/src/main.fab:806-822`), with the recorded reason "the
  `norma:http` server wrappers are not yet wired into the Rust backend"
  (`main.fab:30-33`). **Unverified**: the call forms in `http.fab:70-91` are
  identical to the working local wrappers, so the comment may be stale; not
  proven either way here.
- **examples/** — zero consumers (**known**: repo-wide grep, `*.fab`).
- **hosts/** — no `.fab` consumers; it *is* the provider.
- **radix** — test fixtures only: `package_test.rs:5434` (compile+emit),
  `:5677-5697` (`norma/http` slash spelling diagnostic — unaffected by this
  design), `:5880-5886` (aliased import identity).
- **Blocking consumer gap** (the load-bearing one): the request body arrives
  as valor `octeti` and **cannot be extracted in-language today** — `value ↦ bytes`
  fails codegen, `value ↦ string` fails at runtime on octeti payloads; the
  recorded workaround returns a marker and answers 500
  (`inferentia/src/main.fab:126-133`, `796-799`). `norma/src/valor.fab` has
  predicates/`require_string` but no octeti accessor (**known**: no
  bytes/octeti surface in that file).

### 1.5 Caelum state

`norma:caelum` (`norma/src/caelum.fab` + `caelum/{terminus,connexus,
auscultator}.fab`) is a **type-only sketch**: every method is a `mori` panic
stub ("deferred pending Stage 2 dispatch"). There is **no socket provider in
hosts** — the crates are `aleator, consolum, host-coordinator, host-kernel,
host-native, http, processus, provider-contracts, solum, tempus` (**known**:
`hosts/crates/` listing). The http provider owns its own `std::net`
`TcpListener/TcpStream` directly (`hosts/crates/http/src/lib.rs:18-19`).

### 1.6 Module-directory precedents (the form the operator mandates)

- **caelum** — types-first: `terminus` (tiny shared `Endpoint` genus),
  `connexus` (`Socket`; imports terminus), `auscultator` (`Listener`; imports
  connexus); facade `caelum.fab` holds the constructors and imports the three
  submodules (`norma/src/caelum.fab:28-30`, `caelum/terminus.fab:9`,
  `caelum/connexus.fab:9`, `caelum/auscultator.fab:9`).
- **json** — phase-first: `lexica` (shared internal lexical floor, explicitly
  "Internal to norma:json"), `solve` (parse facade), `pange` (stringify
  facade), `cursor` (shared `ScanResult` value type)
  (`norma/src/json/{lexica,solve,pange,cursor}.fab` headers).
- **solum** — canonical-home: `solum/path.fab` owns path ops; the top-level
  `solum.fab` keeps thin proxies. `path.fab:9-11` documents the loanword rule:
  conventional English names are Grade D loanwords under morphologia — "the
  standard name is the point".
- Package shape: `norma/cista.toml` maps `interfaces = "src"` wholesale, so a
  subdirectory needs no manifest change; `./scripta/check-source` allows
  `*.fab` under `src/` recursively.
- Test placement: per-submodule proba under `norma/exempla/<module>/`
  (`norma/exempla/caelum/{terminus,connexus,auscultator}.proba`).

Facade-file + directory coexistence (`caelum.fab` beside `caelum/`) is live
precedent ×2 (json likewise), so `http.fab` + `http/` resolves today with no
resolver change.

---

## 2. Surface architecture — `norma/src/http/` decomposition

Shape: hybrid of the two precedents — **types-first like caelum** (one genus
per file where the genus is shared currency) plus **pure-codec files like
json/lexica** where the logic is wire framing with no I/O. English leaf names
under the solum/path loanword rule (HTTP vocabulary is the standard name:
Rust `http`, Go `net/http`; type names in English are established in norma —
`Deque` in `fila.fab`, `Response`/`Request` in the current `http.fab`).

```
norma/src/http.fab          facade: constructors + re-exports (caelum.fab role)
norma/src/http/
  headers.fab               Headers genus — case-insensitive fold, multi-value,
                            append/get/values, token/value validation
  request.fab               Request genus — method/path/query/headers/body;
                            path?query split + query field decode
  response.fab              Response genus — status/headers/body; status
                            classes (is_ok/is_client_error/…)
  chunked.fab               chunked transfer codec — pure encode/decode over
                            bytes; no I/O (json/lexica role)
  sse.fab                   SSE event codec — pure format/parse of
                            text/event-stream events (field names, multi-line
                            data, comment lines, \n\n boundaries); event genus
  server.fab                Server/Listener genus — bind, accept loop,
                            keep-alive policy, one-shot + streaming respond
                            (provider `http:*` routes)
  client.fab                Client genus + request verbs — URL split, header
                            assembly, Agent reuse policy, streaming receive
```

Dependency direction (one-way, mirroring caelum):

```
headers ← request ← server
headers ← response ← server / client
chunked ← server / client        (framing under both directions)
sse     ← server / client        (content codec over streaming bodies)
facade imports all leaves and re-exports the public surface
```

### 2.1 Leaf-by-leaf validation (real use, not aspiration)

| Leaf | Earns its file because | Load-bearing consumer |
| --- | --- | --- |
| `headers` | Case-insensitive lookup + multi-value + validation is shared by every other leaf; the provider carrier uses a `[{name,value}]` list that needs folding into a usable genus (`hosts/.../lib.rs:460-476`) | inferentia reads request heads, sets `content-type` on every response (`main.fab:820-822`) |
| `request` | The received-request genus is the server's input currency; query decode is genuine logic | `/generate` body+query handling (`main.fab:466-624`) |
| `response` | Built by server, read by client — must not live in either (else client imports server file) | every inferentia endpoint |
| `chunked` | Pure, hot, testable in-language without sockets; required for any streaming response under HTTP/1.1 | SSE transport (the driver case) |
| `sse` | `text/event-stream` is a real wire protocol (field parsing, multi-line `data:`, comment keep-alives, `id:`/`retry:`); pure and proba-testable | token streaming (the driver case) |
| `server` | Connection lifecycle + keep-alive + streaming writes are provider-coupled state | inferentia `serve` |
| `client` | Verb surface + URL split + reuse policy; distinct from server file so neither imports the other | future inferentia client features, faberlang.dev fetches |

### 2.2 Killed leaves (aspirational — no second caller)

- **`router`** — KILLED. Inferentia dispatches three endpoints by direct path
  equality (`main.fab` route loop); a pattern-matching router genus has zero
  consumers. The current module's own design note already assigns routing
  policy to Faber packages (`norma/src/http.fab:14-17`). Revisit when a
  second server application exists (second-caller rule).
- **`middleware`** — KILLED. Middleware chains are application-framework
  surface (an app or a future web framework module), not transport. No
  caller in any repo today.
- **`body`/`streaming` as a separate leaf** — KILLED as a file; folded into
  `server.fab`/`client.fab`. A standalone "body abstraction" with one
  implementation per direction would be an unearned layer; the shared parts
  that *are* real (chunked framing, SSE codec) already have their own pure
  files.
- **URL genus** — KILLED as its own leaf for v1. Minimal split lives in
  `client.fab` (scheme/host/port/path/query) and `request.fab`
  (path?query). A full URL/percent-codec module is a follow-up if a second
  consumer appears (`norma:codex` percent-encoding stubs exist at
  `codex.fab:64-88` but are `mori`).

---

## 3. Performance story — what "performant" means here

The server is an **inference** server: the expensive operation is the model
forward pass (tens of ms to seconds per token), not the wire. Performance is
therefore **structural**, not micro-optimization:

1. **Streaming without per-token allocation.** Today a response must be
   fully materialized before `http:respond` (`hosts/.../lib.rs:509-524`) —
   impossible for token streams of unbounded length, and it forces the
   transcript into memory. The target shape: per token, the server does
   O(1) work — one SSE frame (`data: …\n\n`) built from the token, one
   provider write call, nothing retained. Two hazards to design against:
   - **Per-token string concatenation is O(n²)**: the current transcript
     joiner is `out ← out + …` per token (`inferentia/src/main.fab:719-727`).
     Streaming removes the need to accumulate at all — each frame is built
     from the current token only. The non-streaming JSON path keeps the join
     (bounded by `max_tokens`), documented as such.
   - **Double-buffering at the seam**: the frame must cross the valor
     boundary once, as `octeti`, not as a rebuilt string
     (`chorda.encode`, `norma/src/chorda.fab:398-403`).
2. **Connection reuse.** Server: keep-alive instead of the hardcoded
   `connection: close` + `shutdown(Both)` per response
   (`hosts/.../lib.rs:236-240`, `:511-512`) — HTTP/1.1 defaults to
   persistent connections, and every OpenAI-compatible client expects it.
   Client: one `ureq::Agent` held per `Client` genus instead of one per call
   (`hosts/.../client.rs:110-112`).
3. **Backpressure.** The natural loop is pull: `accept → generate one token
   → write one frame`. If the consumer stalls, the provider write must
   block (bounded by a write timeout), which stalls `generate` — correct,
   implicit backpressure. Two rules make it honest: **no unbounded queueing
   inside the provider** (the pending map stays bounded by accepted
   connections), and **bounded accept backlog** (excess connections are
   refused, never buffered indefinitely).
4. **Buffer pooling.** This is a **host concern, not a Norma concern**, and
   the design says so honestly: Faber values are value-semantic; pooling
   read/write buffers belongs inside the provider (per-connection buffer
   reuse instead of today's fresh `Vec` + 4096-byte chunk per request,
   `hosts/.../lib.rs:301-306`). Norma's obligation is only to **not force
   copies**: pass `bytes`, pass frame slices, never rebuild strings the
   provider will re-encode.
5. **Concurrency shape.** The 5 ms sleep-poll (`hosts/.../lib.rs:27`) is
   *not* the bottleneck at inference token rates (≤ one event per model
   step); replacing it with async I/O is a provider-internal improvement,
   not a contract change. The contract change that matters: **per-connection
   in-flight state** instead of the single global pending map
   (`hosts/.../lib.rs:37-48`) — N connections × 1 generation each.

**Measurable done-when** (for the wave that lands streaming): per-event
server CPU cost is one provider call plus O(frame) framing; a streaming
response holds O(1) memory in the generated code regardless of token count;
two sequential requests reuse one TCP connection (keep-alive proof); a
stalled reader stalls generation (backpressure proof).

### 3.1 Division of labor — hosts provider vs Norma

| Concern | Owner | Rationale |
| --- | --- | --- |
| TCP accept/read/write, keep-alive socket lifetime, write timeouts, buffer reuse, TLS transport | **hosts `http` provider** | physical effects; already the owner of `std::net` (`lib.rs:18-19`) |
| Chunked framing (encode/decode) | **Norma `http/chunked.fab`** | pure byte transform; testable in-language |
| SSE event codec | **Norma `http/sse.fab`** | pure text protocol; testable in-language |
| Header folding/validation, status classes, query decode, URL split | **Norma** | pure logic |
| Keep-alive *decision* (HTTP/1.1 default-on, honor `connection: close`, 1.0 default-off) | **Norma `http/server.fab`**, enforced via provider write flags | protocol policy is language-visible; the socket stays open only if the provider is told |
| Routing, middleware, request policy | **Faber applications** (unchanged — `http.fab:14-17`) | no second caller |

**Provider contract change (filed as its own need — never silent):**
extend `http:*` with a streaming-respond lifecycle and per-connection state:

```
http:listen   [port, max_body_bytes?, bind_host?] → numerus     (bind_host new; default 127.0.0.1)
http:accept   (handle) → valor                                   (carrier + connection id)
http:respond_open [request_id, status, headers] → writer        (begins a chunked response)
http:respond_chunk [writer, bytes] → vacuum                     (one frame; blocks under backpressure)
http:respond_finish [writer, keep_alive] → vacuum               (terminal chunk + connection policy)
http:respond   (existing one-shot)                               (kept: bounded non-streaming path)
http:stop      (handle) → vacuum                                 (unchanged)
```

Manifest and tests update in the same hosts unit
(`hosts/crates/http/src/manifest.json:6-12`, `src/http_test.rs`). Norma
consumes the new routes only through `http/server.fab`.

---

## 4. Protocol scope — recommendation

### 4.1 HTTP/1.1-complete + SSE for v1 — **upheld** (not overturned)

- **The deployment is loopback/LAN.** The provider binds 127.0.0.1 today
  (`lib.rs:142`); Inferentia's own help text says "loopback only"
  (`main.fab:871`). HTTP/2's wins (multiplexing, HPACK) buy throughput on
  many-parallel-requests over high-RTT links — not the inference serving
  shape (few connections, long-lived streams, ~0 RTT).
- **SSE-over-1.1 is the compatibility floor** of every inference server
  clients actually speak to (OpenAI-compatible streaming is SSE over
  HTTP/1.1). Choosing 2/3 would *reduce* interop, not increase it.
- **h2 requires HPACK** (static+dynamic tables, Huffman coding) plus stream
  state machines; **h3 requires QUIC** (UDP, TLS mandatory). Neither stack
  exists anywhere in the workspace (`caelum` is TCP-shaped and unbacked,
  §1.5). Either would gate this module on a transport that does not exist —
  a textbook unicorn wait.
- **"1.1-complete" is scoped, not maximal**: keep-alive, chunked in both
  directions, Host enforcement, proper status surfaces. Explicitly out:
  `Expect: 100-continue` (no consumer), Range/partial content (no
  consumer), content compression (pass `content-encoding` through
  untouched; never compress SSE — intermediaries buffer compressed event
  streams), WebSockets (a separate future module over the socket layer, not
  HTTP's job).

### 4.2 TLS policy — **explicit-never for the v1 server, provider-owned when it arrives**

- The v1 **server** is plaintext loopback, stated as policy in the module
  header and enforced by the provider binding 127.0.0.1 unless a bind host
  is explicitly passed. Rationale: TLS on loopback is pure cost, and a real
  TLS story (cert sourcing, trust roots, verification-failure policy) is a
  product decision with no v1 consumer.
- The v1 **client** keeps provider TLS (ureq default features) — hitting
  `https://` endpoints is a real client need and costs nothing in-language.
- **Named follow-up** (filed as a need): "hosts-native TLS transport for
  caelum + http". The seam is already sketched on the caelum surface —
  `listen(port, cert?, key?)` and `connect(host, port, tute?)`
  (`norma/src/caelum.fab:36-59`). TLS belongs at that native seam
  (provider-owned), never re-implemented in Norma.

---

## 5. Migration path — clean break (workspace default)

**Facts that make the clean break cheap:**

- Exactly **one** Faber consumer exists and it already bypasses the module
  (local wrappers, `main.fab:806-822`) — nobody imports the current client
  verbs or the interfaces.
- The `norma:http` **import specifier is unchanged** by the directory form:
  `http.fab` remains the facade beside `http/` (caelum/json precedent,
  §1.6), so the radix import-resolution tests (`package_test.rs:5677`,
  `:5880`) are untouched.
- The one green fixture that calls `http.get(...)` (`package_test.rs:5434`)
  keeps compiling if the facade re-exports the client verbs — but see the
  special-case repair below.

**Break, don't shim:**

1. Replace `norma/src/http.fab` with the facade + `http/` directory (unit 1).
   No re-export shim of the *old* verb set beyond what the new surface
   naturally owns; the drifted annotation state (`@ public` on `get` only,
   none on the server verbs — §1.1) is not worth preserving.
2. **Delete or repair the `is_builtin_norma_http` special-case**
   (`radix-program/src/library.rs:1549-1573`): its name set (`petet`,
   `Replicatio`, …) is disjoint from the live module and its target
   (`faber::http`) does not exist. Filed as a radix amendment need. The new
   client verbs ride the ordinary generated-library path (like `get` does
   today) or the `ad` route path — decided in the client wave, not now.
3. **Inferentia migration is its own named unit** (not part of the norma
   waves): swap the local wrappers for `norma:http/server.fab` imports,
   add the SSE endpoint. It is *useful* only when the valor→octeti body gap
   (§1.4) closes — that gap is the real hard gate on in-language HTTP body
   reading, and it is a radix/faber-runtime producer fact, already routed
   there by the inferentia comment (`main.fab:798-799`) but re-filed as a
   visible need so it is not silent.

---

## 6. Caelum relationship — **beside it, not on it** (v1), with a convergence rule

- HTTP rides the `http:*` provider. Caelum is a type-only sketch with **no
  backing provider** (§1.5); building HTTP on caelum would chain the only
  working network surface to a transport that does not exist. This is not a
  close call.
- `norma:http` therefore **imports nothing from `norma:caelum`**. They are
  siblings in the host-effect family (`http.fab:5-6` already says "raw
  sockets, see caelum").
- **Convergence rule for the follow-up**: when a real socket provider lands
  at hosts/native, the http provider re-bases its transport onto the same
  socket primitives caelum exposes (accept/read/write/TLS), so keep-alive
  and TLS policy have one implementation. That is a provider-internal
  refactor, invisible to Norma — which is exactly why deferring it is safe.
- If WebSockets ever ships, that module sits on the socket/caelum seam, not
  inside `norma:http`.

---

## 7. Wave plan (directory form baked into unit 1)

| Unit | Repo / surface | Scope | Done-when |
| --- | --- | --- | --- |
| **H1 — scaffold (directory form)** | norma `src/http.fab` + `src/http/{headers,request,response,chunked,sse,server,client}.fab` + `exempla/http/*.proba` | **Landed** (hand 3db5a792). Facade + seven validated leaves; old single-file surface replaced (clean break); killed leaves (router/middleware/body/url) recorded in the facade header; `@ public` hygiene on the whole surface | proba green for the three genera (import + construct); `import from "norma:http"` resolves in a fixture package; no file named `src/http.fab`-only remains besides the facade |
| **H2 — pure codecs** | norma `src/http/{chunked,sse}.fab` + proba | Chunked encode/decode; SSE format/parse (multi-line data, comments, id/retry, split-boundary tolerance); golden round-trips | proba golden tests pass in-language with zero provider dependency |
| **H3 — provider streaming contract** | hosts `crates/http` (needs the filed hosts need) | New routes `respond_open/respond_chunk/respond_finish`, per-connection pending state, keep-alive, `bind_host`, bounded backlog; manifest + `http_test.rs` coverage (stream, keep-alive reuse, backpressure-block, stop-during-stream) | hosts `cargo test -p http` green incl. new stream/keep-alive tests; manifest lists the new routes |
| **H4 — server module** | norma `src/http/server.fab` + proba/exempla | Listener genus over the extended routes; keep-alive decision; one-shot + streaming respond; SSE serving helper composing `sse.fab` + `chunked.fab` | exemplum server streams N frames over one connection with O(1) in-language memory; backpressure proof (stalled reader stalls the write loop) |
| **H5 — client module** | norma `src/http/client.fab` (+ radix special-case repair, its own need) | Verb surface; URL split; Agent reuse; streaming receive for SSE consumption; delete/repair the drifted `is_builtin_norma_http` mapping | client verb calls compile and run against a local fixture server; streaming receive yields events incrementally (not `read_to_end`) |
| **H6 — inferentia adoption** | inferentia `src/main.fab` | Replace local wrappers with `norma:http/server.fab`; add the streaming generation endpoint (SSE) once the valor→octeti gap (filed need) closes | `/health`, `/model`, `/generate` served through the module; streaming endpoint emits one SSE event per token; campaign open decision "I1 streaming cut line" (`CAMPAIGN.md:108`, `:225`) answered with evidence |

H3 and H2 are independent (parallelizable). H4 needs both. H5's radix repair
is independent of H3 (can run any time after H1). H6 needs H4 + the radix
body-extraction need.

---

## 8. Open decisions routed to mind (filed as needs)

1. **hosts**: extend the `http` provider contract (streaming respond
   lifecycle, keep-alive, bind host, per-connection state) — producer for H3.
2. **radix**: valor→octeti body extraction (`value ↦ bytes` codegen failure;
   `value ↦ string` runtime failure on octeti) — hard gate on any
   in-language HTTP body reading; blocks H6 usefulness.
3. **radix**: delete/repair the drifted `is_builtin_norma_http`
   special-case (`library.rs:1549-1573`).
4. **decision/follow-up**: hosts-native TLS transport for caelum + http
   (explicit-never for the v1 server; provider-owned when it lands).
5. **inferentia campaign**: the "I1 streaming cut line" open decision gets
   its answer from H4/H6 — amend the campaign when evidence lands.

## 9. Not claimed / unverified

- Whether `norma:http` server wrappers actually fail to bind in generated
   Rust today (`main.fab:30-33` claims not-wired; call forms look identical
   to the working local wrappers — not proven either way).
- ureq default-feature TLS behavior (inferred from `Cargo.toml`, not
   exercised).
- The parallel auditor's consumer-gap enumeration (`af1de6d7`) had **not
   landed** in mail when this design was written; this doc proceeds
   independently per the task's instruction. Reconcile if it lands later.
- No performance measurements were taken; §3 states structural targets and
   the measurable done-whens, not results.
