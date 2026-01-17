# Maintaining `crude-context`

This repo is designed for **progressive disclosure**: keep the entrypoint lightweight and link to deeper docs when needed.

## Goals

- **Fast bootstrap**: agents can orient in <2 minutes.
- **Low token footprint**: the default path stays short and stable.
- **Actionable**: links lead to concrete next steps (commands, checklists, pointers).
- **Ecosystem-friendly**: patterns scale as more language/framework repos are added.

## What Goes Where (Decision Criteria)

### Put it in `README.md` if it is…

- Needed for **most tasks** across **most repos**
- **Stable** (low churn / not date-sensitive)
- **Short** (scannable; no big tables or long command transcripts)
- Primarily **navigational** (a map + “if you’re doing X, read Y”)

### Put it in a linked document if it is…

- Relevant only for a **subset** of tasks (debugging, releases, spec work, etc.)
- **Long-form** reference material (procedures, command catalogs, exhaustive lists)
- **High churn** (status, sequencing, priorities)
- Easier to consume as **lookup** than always-loaded background

### Split a document when…

- A section stops being scannable in ~30 seconds
- It mixes audiences (e.g. SDK authors vs. spec authors vs. app builders)
- It’s both **important** and **volatile** (keep a stable summary + link to “current”)

## Structure Conventions

- **One obvious entrypoint**: `README.md`.
- **Keep bulk docs under `docs/`** (leave only entrypoints at the root).
- **Name by intent**: `docs/architecture.md`, `docs/repo-map.md`, `docs/debugging.md`, `docs/releasing.md`, `docs/glossary.md`, etc.
- **Prefer patterns over lists** when repos are formulaic (e.g. language SDKs).
- **Call out volatility** in docs that change often (e.g. “high-churn” banner + last-updated date).
- **Keep private details out**: no credentials, secrets, tokens, or internal-only endpoints.

## Source of Truth

- Prefer linking to canonical sources when they exist:
  - OpenAPI spec files / test suites for protocol truth
  - GitHub Issues for day-to-day work coordination (eventually public)
  - Submodule `DEVELOPING.md` for repo-specific dev loops
