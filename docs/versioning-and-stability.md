# Versioning and stability

Crude is currently **unstable** across the ecosystem. We expect aggressive iteration and are comfortable making breaking changes.

This document explains how to think about:

- Stability (what you can rely on)
- Backwards compatibility (when we promise it)
- Versioning (how we express change in each ecosystem)

## Current status: unstable

Right now, treat all Crude repos and artifacts as **unstable** unless a repo explicitly says otherwise.

Practical implications:

- You should expect breaking changes (including commit-to-commit breakage).
- There is no deprecation policy yet (we will generally remove/rename freely until there are real users).
- Anything undocumented should be assumed unstable.

## Libraries vs apps

Crude artifacts fall into two broad classes:

- **Libraries**: intended to be depended on by other code (breakage means code changes).
- **Apps**: intended to be used by humans/agents via UI/UX (breakage is often “changed expectations” rather than compile errors).

Some artifacts are “gray area” (e.g., MCP servers, specific plugins). Default to treating them as **app-like** unless there is a clear reason to treat them as libraries.

## Protocol: “versionless”, profile-driven

The Crude protocol is intended to be “versionless” in the same broad sense as many web standards: it evolves over time, and implementations describe what they support via feature sets/profiles and (eventually) a support matrix.

This repo does not define those profiles yet. Expect this to emerge in the spec repo (today: `crude-openapi`, expected to grow into a broader “spec + conformance” source of truth).

## Stability vocabulary (inspired by StableVer)

We do not currently apply a formal “StableVer” scheme universally, but the _stability concepts_ are useful. The intent is to make stability explicit over time, feature-by-feature.

Useful labels:

- **Stable**: we will not make breaking changes to this feature without a deliberate compatibility process.
- **Beta / stabilizing**: intended to become stable; changes may happen, but should come with guidance.
- **Deprecated**: slated for removal; a migration path should exist.
- **Internal**: not intended for external use; may change without warning.
- **Undefined**: behavior that isn’t specified or adequately documented; treat as unstable.

For now, most of Crude is effectively in **unstable/undefined** territory.

## Versioning conventions (by ecosystem)

Crude does not force a single versioning scheme across all repos. We follow the conventions of each ecosystem, while keeping the _stability intent_ clear in documentation.

### npm / JS / TS libraries (SemVer-compatible)

- Use SemVer-compatible versions (typically `0.x` today).
- While unstable, do not assume SemVer provides safety: breakage may occur without any particular bump discipline.

### Go modules (semantic import versioning)

- Use Go’s semantic import versioning conventions.
- Today, many modules may not include a `/vN` suffix (which Go tooling treats as `v1`), but **that should not be interpreted as “stable” yet** in the Crude ecosystem.

### Swift packages (SemVer)

- Use SemVer-compatible versions (typically `0.x` today).
- Treat versions as informational while unstable; do not assume strict compatibility guarantees yet.

### Apps (CalVer)

For app-like artifacts, prefer CalVer.

Guideline:

- `YYYY.MM.micro`
  - `YYYY` is the 4-digit year.
  - `MM` is the month (prefer zero-padded when possible).
  - `micro` starts at `0` and increments.

Exact formatting depends on the publication channel’s constraints (for example, some registries effectively force “SemVer-shaped” version strings).

## Repo expectations (minimal)

Every repo that publishes artifacts should include a short “Status / Stability” section in its `README.md` that:

- States whether the project is unstable today.
- Links to this doc for ecosystem-wide guidance.

## Open questions (intentionally deferred)

- What are the concrete gates for moving features from unstable → stabilizing → stable?
- How will we publish protocol profiles/feature sets and a support matrix?
- When do we introduce deprecation periods and migration guarantees?
