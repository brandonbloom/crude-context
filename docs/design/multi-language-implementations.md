# Multi-language implementations (design philosophy)

Crude is designed so that *independently implemented components* (in different languages) can interoperate.

This document captures the intent for future language implementations (including “minimal” plugins), and how that intent interacts with spec generation and optional features.

## Boundary rule: HTTP + OpenAPI is the system interface

- Between components (different repos / different languages / different processes), the interface is always HTTP and the OpenAPI contract.
- Do not couple implementations by importing each other’s internal libraries across language boundaries.
- Treat the OpenAPI spec + protocol conformance tests as the canonical contract for interoperability.

See also:
- Protocol truth (today): `protocol-contract.md`
- Conformance suite: `conformance.md`

## Within a language: idiomatic (within reason) abstractions

Inside a single language ecosystem, the server implementation should operate on idiomatic abstractions rather than “raw HTTP handlers everywhere”.

Typical shape (names vary by language):

- `Domain` → owns domain metadata and access to collections.
- `Collection` → lists elements, gets an element by key, declares supported formats.
- `Element` → describes the item and provides access to its content (read and possibly write).

The HTTP server layer should be primarily an adapter: parse URL/method/headers, call these abstractions, and map results to protocol responses.

## Recommended two-layer architecture: protocol layer + idiomatic layer

Most ecosystems should converge on a **two-layer** pattern:

1) **Protocol / DTO layer** (HTTP-idiomatic, minimal runtime)
   - **Generated from OpenAPI** (or a deterministic transform of it).
   - “Awkward/low-level is fine”: it’s effectively “curl in <language>”, and it matches the OpenAPI boundary.
   - Should be reproducible; avoid hand-written drift.
   - Applies to both server adapters (request/response mapping) and HTTP-backed clients (protocol client).

2) **Idiomatic layer** (ergonomic, plugin-author facing)
   - Domain model interfaces (`Domain` / `Collection` / `Element`, or language equivalents) and helpers.
   - This is where ecosystems invest in ergonomics (query builders, filter DSLs, convenience methods, capability checks).
   - Should be implementable by both:
     - servers/plugins (in-process implementations), and
     - clients (HTTP-backed implementations that delegate to the protocol client).

If you’re unsure where something belongs: default to “protocol layer” if it’s about *wire shapes / HTTP semantics*, and “idiomatic layer” if it’s about *developer ergonomics / in-process modeling*.

## Per-language bindings: recommended layering

Most language repos should converge on a similar internal layering, even if names differ:

1) **Generated protocol types** (DTOs, “protocol layer”)
   - Types generated from OpenAPI (or hand-written equivalents).
   - These should be “dumb” data shapes: no I/O, minimal behavior.
   - Prefer checking these into the repo when generation is not universally available in consumer environments.

2) **Protocol client and/or server adapter glue** (still “protocol layer”)
   - For clients: a low-level HTTP client that is directly typed by the generated protocol types.
   - For servers: router/handler glue that maps HTTP/OpenAPI concerns to idiomatic interfaces.

3) **Core model interfaces** (the idiomatic layer)
   - `Domain` / `Collection` / `Element` interfaces (or protocols / traits).
   - Optional capabilities expressed idiomatically (see below).
   - These are what plugin authors implement.
   - Keep these interfaces as “pure” as practical, but accept that some protocol-adjacent concepts (like `Format`) are part of the domain model because they drive content negotiation.

4) **Idiomatic clients / engines** (the idiomatic layer)
   - Higher-level clients that expose idiomatic `Domain`/`Collection`/`Element` (or equivalents) to consumers.
   - Can be backed by:
     - an in-process implementation (useful for testing and composition), or
     - the protocol client (HTTP-backed, “remote engine”).
   - Should tolerate partial implementations (progressive enhancement).

The key rule: plugin authors should spend most of their time implementing the **idiomatic interfaces** (3), not wiring HTTP/protocol glue (2).

### DTO separation (recommended)

Generated OpenAPI DTOs should be kept separate from the idiomatic interfaces so they can evolve independently.

- Expect some O(N) copying/mapping at the server adapter boundary.
- Prefer central “mapping” helpers rather than scattering DTO construction throughout plugin code.

### Naming: avoid protocol/idiomatic collisions (recommended)

Protocol DTO names frequently collide with idiomatic types (`Domain`, `Collection`, `Element`, etc.). Prefer a consistent convention:

- Use `*Model` (or language equivalent) for protocol DTOs: `DomainModel`, `CollectionModel`, `ElementModel`, `PageModel`, etc.
- Reserve the short names (`Domain`, `Collection`, `Element`) for idiomatic interfaces.

Allow exceptions when the protocol shape is also the idiomatic value (for example, `ProblemDetails` that is “just JSON”), but default to `*Model` first and add aliases later only if there’s clear value.

## Dependency injection: servers take domains (not globals)

Server implementations should accept domain(s)/collections/elements via dependency injection:

- Most implementations should be able to provide a single, static, preconfigured domain.
- Richer implementations may provide a registry/router of domains (or dynamic domain loading).
- Avoid “the server owns the world” designs where business logic is embedded in routing glue.

This makes it feasible to:
- Implement a domain/collection as a small plugin package.
- Reuse the same domain implementation behind different server front-ends (HTTP, CLI, FS adapters).

### “Static single-domain” should be trivial

Most plugin authors should be able to stand up a server by supplying a single domain value:

- `server := NewServer(WithDomain(myDomain))`
- `server := NewServer({ domain: myDomain })`
- `server := NewServer(myDomain)`

Multi-domain should be supported, but it should not be the first thing a new implementation forces on users.

### Multi-domain without complexity: inject a “domain source”

To keep single-domain easy while still supporting multi-domain servers, it often helps to have the server adapter depend on an “engine” / “domain source” abstraction:

- `listDomains() -> Page<Domain>`
- `getDomain(name) -> Domain?`
- `getRootDomain() -> Domain`

Then:

- A simple plugin can implement the domain source with a single preconfigured domain.
- A richer plugin can implement it with a registry/router, dynamic loading, or composition.

This keeps routing glue stable and pushes variation into a small injectable boundary.

### The “universe” domain and `_universe` sentinel

In multi-domain (“monolith”) servers, the root object is often called the **universe**: a domain-like object that can enumerate or union other domains.

Some bindings may choose to model this explicitly:

- Use a reserved domain key `_universe` (system domain) to represent “the universe”.
- If a plugin supports “universe reflection”, it is OK (and encouraged) for `_universe` to appear in `listDomains` and to act as a convenience sentinel for routes that require a `{domain}` parameter.
  - Example intent: handle domain-scoped routes uniformly by allowing `{domain}=_universe` to mean “operate at the root/unscoped level”.

This is a convenience for binding authors and routers; it should not be required for minimal plugins.

## Progressive enhancement: not every server implements every feature

Crude is a progressive enhancement design:

- Clients should expect some endpoints, media types, or behaviors to be unavailable on a given server.
- Functionality can be *enriched* by proxies that sit in front of simpler servers.

This is why a “minimal plugin server” can be valuable even if it implements only a slice of the full protocol.

### Enriching proxies

An enriching proxy may:
- Route requests to multiple backing servers (possibly implemented in different languages).
- Introspect backing servers to learn what they support.
- Add higher-level behaviors (or fill gaps) without changing backing servers.

**Go is special** in the ecosystem because it is expected to host the most feature-rich implementation(s) and to be the recommended deployment strategy for feature enrichment.

## Example: converter-driven `Accept` enrichment

Example scenario:

- One server provides a collection of Mermaid diagram *source* (e.g. `text/markdown` or `text/plain`).
- Another server provides converter(s) that can render Mermaid source to an image (e.g. `image/png`), potentially implemented in JavaScript using Mermaid tooling.
- A Go proxy sits in front, discovers/introspects these capabilities, and when a client requests `Accept: image/png`:
  - Fetches the underlying source from the diagrams server.
  - Applies an appropriate converter.
  - Returns the enriched representation to the client.

The backing diagrams server does not need to understand `Accept: image/png` to participate.

## OpenAPI “slices” and conformance profiles

The OpenAPI spec is evolving toward separable slices of functionality so that:

- A minimal plugin can implement “just enough” to be useful.
- A proxy can provide additional slices by composition and enrichment.
- Implementations can scale up to the full protocol over time.

Today, the spec generator already supports multiple shapes via flags (see `crude-openapi generate --help`), notably:

- `--domains` / `--no-domains`: include/exclude the domain-scoped `/domains/{domain}/...` surface
- `--root-domain`: include the root (unscoped) `/collections/...` surface

Both shapes are expected to be useful:

- Many simple plugins will be “single-domain”, but may choose either:
  - domain-scoped endpoints (and return exactly one domain from `GET /domains`), or
  - root-domain endpoints only (no `/domains/{domain}` at all).
- Some plugins will be “monolith” servers with multiple domains (for example, “one domain per backing system”).

As slices mature, the intent is that conformance should also become profile-based (so minimal servers can be “conformant” to the slice they claim to implement).

## Optional features and “downcasting” patterns

Because many features are optional, server-side abstractions should support “capability discovery” in an idiomatic way.

Patterns vary by language:

- Go: prefer small interfaces + type assertions (`x.(Fooer)`) or “optional interface” patterns.
- JavaScript/TypeScript: use object shapes with optional members or wrapper factories that attach optional methods.
- Static OO languages: consider `CanFoo() -> bool` paired with `Foo()` that may throw an `Unsupported` error when absent.

The goal is to keep the common path ergonomic while allowing richer servers/proxies to opt into advanced capabilities.

### Guidance for binding authors

- Prefer a small “required core” plus optional capability interfaces over a single huge interface.
- Avoid “fake implementations” (methods that exist everywhere but usually throw) unless that’s the ecosystem norm.
- Make “not supported” a first-class outcome in the server adapter so plugin authors don’t need to think about HTTP status codes.

Because Crude has a lot of optional functionality, ecosystem norms may need adjustment:

- If “optional members everywhere” becomes too loose, consider grouping optional features into capability objects/modules (still discoverable via idiomatic downcasting).
- If “one giant interface” becomes too heavy, split by concern (search, content I/O, formats, writes, converters) and keep the required core minimal.

### Content I/O shape (pragmatic)

Bindings should pick an idiomatic way to represent content bodies:

- Go: `io.Reader` / `io.Writer` (streaming), or `[]byte` for simplicity.
- JS/TS: `Blob`/`ArrayBuffer` (and optionally `string` for text).
- Swift: `Data` / `ByteBuffer`.

If the ecosystem makes streaming awkward, start buffered and add streaming later; keep the core model stable so it doesn’t infect plugin code.

### Content negotiation: avoid raw `Accept` in core interfaces

Bindings should avoid pushing raw HTTP header values (like `Accept: ...`) into plugin-author interfaces.

Recommended approach:

- Model **native** and **supported** formats explicitly (e.g. `Format`/`FormatKey` and a `listFormats()`-style capability).
- Model conversion/formatting explicitly (formatters and/or converters), so a plugin can say “I can produce X” without parsing HTTP.
- Keep header parsing and selection logic in the **server adapter**:
  - Parse `Accept`.
  - Choose a `FormatKey` (or fall back to the collection’s native format).
  - Invoke a formatter/converter pipeline when available.

This keeps plugin code portable and makes it easier for proxies to enrich behavior without forcing every server to reimplement HTTP negotiation logic.

### Errors: internal vs external, and “unsupported”

Bindings should make it easy for adapters to distinguish:

- **External/protocol errors**: not found, unauthorized, method not allowed, unsupported media type, unsupported feature, invalid input.
- **Internal errors**: bugs, panics, unexpected exceptions, backend outages, etc.

Common pattern:

- Define a small, typed error taxonomy for “expected” external failures (including `Unsupported`), and let everything else be treated as internal.
- Let the server adapter own the mapping to HTTP/Problem Details.
- Allow a “debug/admin mode” configuration where internal errors can be surfaced with more detail, while default behavior stays conservative.

## Concrete references (existing patterns)

- Spec slicing flags live in: `crude-openapi/src/spec/api.ts` and `crude-openapi/src/cli/generate.ts`
- Go “optional interface” approach shows up in: `crude-go/core/search.go`
- Go “universe” domain pattern shows up in: `crude-go/core/universe.go`
- Go HTTP/Problem Details mapping patterns show up in: `crude-go/internal/server/errors.go`
- JS/TS optional handler surface shows up in: `crude-js/packages/crude-server/src/index.ts`

## JS/TS example (protocol types + protocol client + idiomatic layer)

This is an example of how the two-layer pattern can look in a JS/TS ecosystem; the same shape generalizes to other languages/tooling.

**Protocol / DTO layer (generated, reproducible)**

- Generate protocol types with `openapi-typescript` (zero runtime) and check the generated typings into the repo.
- Build a low-level protocol client with `openapi-fetch`, typed by those generated protocol types.

**Idiomatic layer (ergonomic, reusable)**

- Define idiomatic interfaces (`Domain` / `Collection` / `Element`) and capability checks.
- Provide an HTTP-backed implementation (an “engine”) that delegates to the protocol client.
- Optionally provide an in-process implementation for tests/composition.

Minimal sketch:

```ts
// protocol/types.ts (generated; checked in)
export type { paths as ProtocolPaths } from "./generated/openapi";

// protocol/client.ts (low-level, “curl in TS”)
import createClient from "openapi-fetch";
import type { ProtocolPaths } from "./types";
export const protocol = createClient<ProtocolPaths>({ baseUrl: process.env.CRUDE_URI });

// idiomatic/http-engine.ts (ergonomic layer backed by protocol client)
export function createHttpEngine(protocolClient: typeof protocol): Engine { /* ... */ }
```

## OpenAPI “generics” and codegen compatibility

Some OpenAPI/JSON Schema specs express “generics” (for example `Page<T>`) using JSON Schema 2020-12 features like `$dynamicRef` / `$dynamicAnchor`. Not all generators handle these well.

Recommended approach:

- Keep the spec “true” where possible (the OpenAPI/JSON Schema is the source of interoperability).
- If a chosen generator cannot handle dynamic-ref generics, allow a **target-specific generation mode** (for example, “erase generics” / “instantiate generics”) that produces codegen-friendly schemas/types.
- Document the decision in the target repo (what mode is used and why), and keep protocol conformance stable (the mode must not change observable wire behavior).

## Open questions (to resolve in specs + tests)

These are mostly protocol-spec questions (tracked in `crude-openapi/PLAN.md`) and shouldn’t block per-language bindings.

- What are the recommended status codes / Problem Details shapes for “feature not supported” vs “resource not found”?
- Which features are domain-level vs collection-level vs element-level capabilities (search, organizer, formats, converters, writes)?
