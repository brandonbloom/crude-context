# Architecture (Crude)

## Key Idea

Crude components communicate via an **OpenAPI-defined protocol**. Treat the spec as the primary contract: implementations should align on behavior even when codebases differ across languages/frameworks.

## HTTP-First + Enriching Proxies

Crude takes HTTP seriously and expects **proxies** to provide enrichment such as security, functionality, and customization (rather than baking everything into each implementation).

## Cross-Ecosystem Shape

Language-centric repos are expected to converge on a familiar set of components (names and layout may vary across ecosystems; e.g. a TypeScript package might be named crude-server while Swift might use CrudeServer):

- Core primitives and shared domain logic
- Client implementation of the protocol
- Server implementation of the protocol
- Examples and conformance fixtures (including spec-defined debug collections where applicable)

## Docs Note

`crude-docs` is human-targeted documentation. This `crude-context` repo can contain denser, agent-oriented notes, but should still follow progressive disclosure (see `docs/maintaining-context.md`).

Related design notes: `design/plugin-system.md`.
