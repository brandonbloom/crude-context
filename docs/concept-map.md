# Concept map (dense)

This is a compact, agent-oriented map of the Crude system. For fuller narrative docs, see `crude-docs/docs/concepts/*`.

## The core hierarchy

Domain → Collection → Element

- A domain is a scoped namespace for a data source or application vocabulary.
- A collection is a homogeneous set of elements within a domain.
- An element is a keyed item within a collection.

## The HTTP surface (shape, not exhaustiveness)

Discovery and navigation:

- List domains
- Get a domain
- List collections in a domain
- Get a collection
- List elements in a collection (supports cursor + limit; may support filter)
- Get an element

Universal vs scoped forms:

- Many resources have both:
  - Scoped under a domain (for example, `/domains/{domain}/...`)
  - Universal forms (for example, `/collections/{collection}/...`)

Content and formats:

- “Content” endpoints represent the natural representation of a collection or element.
- Formats are enumerated as resources and accessed via content negotiation.

Navigation UI support:

- Organizer is a hierarchical navigation structure suitable for sidebars, filesystem views, etc.
- Organizer entries use `ref` strings (for example, `collection:alphabet`) to point at targets.

Search:

- Search returns heterogeneous results with a relevance score.

## Canonical truth (in priority order)

When something is unclear or docs conflict:

1) Generated OpenAPI spec (what’s defined): `crude-openapi/build/spec.json`
2) Conformance suite (what’s required): `crude-openapi/src/tests/hurl/*.hurl`
3) Repo-specific developer docs (`DEVELOPING.md`, `mise.toml`)
4) Human docs (`crude-docs/docs/*`) and design notes in this repo

## Minimal “known-good” behavior

The shared Hurl suite exercises a required debug domain and a small set of behaviors:

- Domain discovery for `debug`
- Listing/getting collections and elements (including pagination)
- Problem Details on missing resources
- Organizer shape and refs
- Content negotiation on debug collections

See: `docs/design/conformance.md`

