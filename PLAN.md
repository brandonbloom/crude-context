# crude-context: Plan

This plan is the single-threaded, forward-looking checklist for improving shared agent context across the Crude ecosystem.

## How We Use PLAN.md Files

- A plan is a living document for the **main work** to do in a repo.
- Keep it **mostly forward-looking**; include history only to clarify current decisions.
- Prefer a **single core thread** of work (sequence), with limited parallel subtracks.
- Use checklists for execution, and **compact/purge** completed work periodically.
- Link out (issues/PRs/docs) instead of duplicating long discussions.
- When the plan becomes large, split details into focused docs and keep this file as the index.

## Current Priorities

### 1) Make shared context discoverable everywhere

- [x] Ensure every submodule has an `AGENTS.md` pointing to `crude-context`
- [x] Provide a verification script in the coordination repo
- [x] Provide a submodule `AGENTS.md` template

### 2) Establish a progressive-disclosure doc structure

- [x] Keep `README.md` as the entrypoint
- [x] Keep bulk docs under `docs/` with recursive `README.md` indexes
- [x] Create guidance for maintaining/iterating on context docs
- [x] Move legacy design notes from the coordination repo into `docs/design/`

### 3) “Slurp” cross-repo knowledge that helps other repos

Create concise, agent-oriented docs that summarize shared truths and link to canonical sources.

- [x] Document the protocol contract (what exists today) and where it lives
  - Sources: `crude-openapi` generated spec + meta-spec sources
- [x] Document the conformance baseline (Hurl suite) and how to run it everywhere
  - Sources: `crude-openapi/src/tests/hurl/*`, `crude-openapi test`
- [ ] Document codegen workflow patterns across ecosystems (OpenAPI → generated bindings)
  - Sources: `*/mise.toml`, `*/DEVELOPING.md`, repo-local generation scripts
- [x] Document cross-repo tooling conventions (mise task contract)
- [ ] Create a shared “ports/URIs/env var registry” doc (local-first)
  - Sources: `crude-web`, `crude-cli`, `crude-go`, `crude-swift`
- [ ] Capture repo patterns that should converge (and acceptable variations)
  - Sources: `crude-js` package structure, other language repos over time

## Later / TBD

- Release strategy for semver ecosystems (trunk development + publishing)
- Planning/sequencing model beyond issues (keep this repo’s plan short either way)
- Security/compliance guidance once relevant
