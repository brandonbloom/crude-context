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

- Architecture overview: `docs/architecture.md`
- How to iterate on this repo’s docs: `docs/maintaining-context.md`
- Current work plan for this repo: `PLAN.md`
