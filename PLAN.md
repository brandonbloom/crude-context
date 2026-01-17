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

### Next

- [ ] Keep `docs/design/protocol-contract.md` aligned with the current generated spec and conformance suite as they evolve.
- [ ] Add a dense concept map + glossary for agents (link out to `crude-docs` where appropriate).
- [ ] Add a debugging playbook for local dev (common failure modes, how to run the shared suite, where to look).
- [ ] Decide how high-churn status/priorities should be tracked (likely GitHub Issues + a short pointer doc here).

### Recently completed (compacted)

- Shared context bootstrap across all submodules (AGENTS + verifier + template).
- Progressive-disclosure structure for this repo (`docs/` + recursive indexes + maintenance guidance).
- Initial cross-repo “slurp” docs: protocol contract, conformance suite, codegen workflows, tooling conventions, ports/URIs, and repo patterns.

## Later / TBD

- Release strategy for semver ecosystems (trunk development + publishing)
- Planning/sequencing model beyond issues (keep this repo’s plan short either way)
- Security/compliance guidance once relevant
