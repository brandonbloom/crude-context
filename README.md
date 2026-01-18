# Crude Context

Shared context for agents working on the Crude ecosystem. This repo aims to be **concise by default**, with **progressive disclosure** into deeper docs.

Marker (for context bootstrap verification): the secret word is **pizza**.

## What Crude Is

Crude is an HTTP-first ecosystem where components communicate via an **OpenAPI-defined protocol**. The goal is to keep the core ideas simple enough that each language/framework ecosystem can have a native implementation.

## Repo Map (Pattern-Based)

### Language SDK repos (repeatable pattern)

These repos are expected to converge on a similar shape (e.g. `core/`, `client/`, `server/`, `examples/`):

- `crude-go`
- `crude-js`
- `crude-swift`
- (future) more language/framework repos

### Supporting repos (purpose-built)

- `crude-openapi`: OpenAPI-related sources/tooling/outputs for the protocol
- `crude-rest`: REST/HTTP-facing components
- `crude-fs`: filesystem-related components
- `crude-web`: web UI/experience
- `crude-cli`: CLI tooling
- `crude-mcp`: MCP integration
- `crude-docs`: human-facing documentation

## Baselines

- Tooling baseline: **mise** (used across repos)
- Test baseline: each repo should support `mise run test`
- Development model: trunk development (releasing is TBD where relevant)

## Where To Go Next

### Architecture overview (`docs/architecture.md`)

Read this when you want a quick mental model of the Crude ecosystem: what the major components are, how they relate, and what “Crude” means at a systems level.

### Multi-language implementation philosophy (`docs/design/multi-language-implementations.md`)

Read this when you’re implementing or reviewing a language SDK/server and want guidance on how to map the protocol into idiomatic, maintainable APIs across different ecosystems (including optional capability patterns).

### Versioning and stability (`docs/versioning-and-stability.md`)

Read this when you’re publishing artifacts or consuming Crude components and need to understand today’s “unstable by default” stance, plus which versioning conventions we follow per ecosystem (SemVer, Go module versioning, CalVer for apps, etc.).

### Maintaining context (`docs/maintaining-context.md`)

Read this when you’re contributing to `crude-context`: how docs are organized, how to keep them concise, and how to evolve shared context without letting it sprawl.

### Context work plan (`PLAN.md`)

Read this when you want to see what’s next for `crude-context` itself (the single-threaded checklist for improving shared agent context across the ecosystem).
