# Architecture (Crude)

## Key Idea

Crude components communicate via an **OpenAPI-defined protocol**. Treat the spec as the primary contract: implementations should align on behavior even when codebases differ across languages/frameworks.

## HTTP-First + Enriching Proxies

Crude takes HTTP seriously and expects **proxies** to provide enrichment such as security, functionality, and customization (rather than baking everything into each implementation).

## Multi-language intent

- The system boundary is always HTTP + OpenAPI; do not couple implementations via cross-language internal imports.
- Within a language, implementers should create idiomatic abstractions (Domain/Collection/Element) and inject them into the server as dependencies.
- Not every implementation needs every feature: Crude is designed for progressive enhancement via enriching proxies.

See: `design/multi-language-implementations.md`.

## Cross-Ecosystem Shape

Language-centric repos are expected to converge on a familiar set of components (names and layout may vary across ecosystems; e.g. a TypeScript package might be named crude-server while Swift might use CrudeServer):

- Core primitives and shared domain logic
- Client implementation of the protocol
- Server implementation of the protocol
- Examples and conformance fixtures (including spec-defined debug collections where applicable)

## Docs Note

`crude-docs` is human-targeted documentation. This `crude-context` repo can contain denser, agent-oriented notes, but should still follow progressive disclosure (see `docs/maintaining-context.md`).

Related design notes: `design/plugin-system.md`.
