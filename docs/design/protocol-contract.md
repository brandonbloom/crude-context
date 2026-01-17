# Protocol contract (today)

This document summarizes the **current** Crude HTTP/OpenAPI contract as implemented and tested today. Treat the generated OpenAPI spec and conformance suite as canonical.

## Canonical sources

- Full generated spec: `crude-openapi/build/spec.json`
- Meta-spec sources: `crude-openapi/src/spec/types.ts`, `crude-openapi/src/spec/api.ts`
- Conformance suite: `crude-openapi/src/tests/hurl/*.hurl` (run via `crude-openapi test`)

## Resource model

- Domains → collections → elements.
- Most resources have “universal” and “scoped” forms:
  - Scoped: `/domains/{domain}/collections/{collection}/...`
  - Universal: `/collections/{collection}/...` and `/search`, `/organizer`, etc.

## Paths and operations (high-level)

The current spec is mostly read-only:

- Listing + introspection via GET:
  - Domains: list/get, organizer, search
  - Collections: list/get, list elements, list formats
  - Elements: list/get
  - Content endpoints for collections and elements
- Writes (currently):
  - PUT element content: `/.../elements/{element}/content`

## Pagination

- List endpoints return a page object containing:
  - `items`: array
  - `next`: cursor or null
- Query params:
  - `cursor`: opaque pagination token
  - `limit`: integer limit

## Filtering

- List-elements endpoints accept a `filter` query parameter.
- The filter is a string; semantics are described in human docs, but concrete syntax can vary by client.
  - See: `crude-docs/docs/concepts/filtering.md`

## Content negotiation and “content” endpoints

- “Content” endpoints represent the natural/primary representation of a collection/element.
- Clients use `Accept` to request specific media types.
- The conformance suite currently exercises `application/vnd.crude.lines` on debug collections.

## Errors

- Resource-not-found cases are represented as Problem Details (`application/problem+json`).
- See conformance expectations in `crude-openapi/src/tests/hurl/elements.hurl`.

## Notes on drift vs. aspirational docs

Some docs in `crude-docs` describe the broader vision (“CRUD engine”, properties, etc.). When in doubt, prefer:

1) `crude-openapi/build/spec.json` (what’s defined)
2) `crude-openapi/src/tests/hurl/*.hurl` (what’s required by tests)
3) Implementation docs per repo (`DEVELOPING.md`)

