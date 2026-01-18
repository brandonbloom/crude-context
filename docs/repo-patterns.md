# Repo patterns (convergence + allowed variation)

This document describes conventions that should converge across Crude repos, plus variations that are acceptable (or expected) due to ecosystem differences.

## Converge on these

### Tooling and tasks (mise)

- Use mise as the standard entrypoint for tool installation and common tasks.
- Prefer a consistent task surface:
  - `mise run generate` (if the repo has generated artifacts)
  - `mise run test`
  - `mise run server` (if applicable)
  - `mise run lint` and `mise run format` (if applicable)
  - `mise run ci`

### OpenAPI-first boundary

- When repos interact across language boundaries, prefer the OpenAPI HTTP interface over importing internals from another repo.
- Generated OpenAPI artifacts should be reproducible and not hand-edited.
- Prefer a two-layer split in each ecosystem: a protocol/DTO layer generated from OpenAPI, and a separate idiomatic layer that plugin authors implement and higher-level clients use.

### Idiomatic internals + dependency injection

- Server packages should expose idiomatic Domain/Collection/Element (or equivalent) abstractions for that language.
- HTTP routing/adapters should depend on those abstractions via injection (a static single-domain configuration should be the easy path).
- Optional features should be modeled idiomatically (small optional interfaces, optional members, capability checks), not by forcing every implementation to “fake” the full surface.
  - When protocol DTO names collide with idiomatic types, prefer a `*Model` naming convention for protocol DTOs by default.

### Conformance mindset

- Server implementations should be able to run the shared Hurl suite (via `crude-openapi test`) as part of `mise run test` where applicable.
- Protocol-wide behavior changes should land with corresponding conformance updates.

### Documentation shape

- Each repo has a small set of stable entrypoints:
  - `README.md`: what it is + how to run the most common workflows
  - `AGENTS.md`: where to find shared context + repo-specific constraints
  - `DEVELOPING.md`: deeper developer workflow details (optional, but encouraged)
  - `PLAN.md`: forward-looking “single-thread” plan (optional, but encouraged)

## Expected variation

### Packaging and layout

- Some ecosystems will be monorepos (multiple packages) while others will be single-package repos.
- Folder and symbol naming will vary by language (for example, server/client components may be separate packages rather than directories).
- Generated artifacts may live in different locations depending on the codegen tooling.

### Build systems

- Go: `go generate`, `go test`, Makefiles, build tags
- Swift: SwiftPM conventions, Xcode integration, SwiftLint
- JS/TS: Bun or Node toolchains, monorepo tasking, type generation pipelines
- Web UI: dev servers and adapters specific to the chosen framework

### Ports and dev URLs

- Local ports can differ by repo; document the defaults and how to override them.
- Prefer env-driven configuration (for example, `CRUDE_URI`) over hardcoding.

## When to standardize

Standardize when it reduces cross-repo friction:

- Tasks that agents and CI run everywhere
- How protocol changes propagate (spec → bindings → tests)
- How to run the shared conformance suite

Avoid forced standardization when it fights an ecosystem’s norms (layout, build tool choice, package structure), as long as the task surface and protocol contract stay consistent.
