# Conformance (Hurl suite)

Crude uses a shared, protocol-level HTTP regression suite written in Hurl. Any server implementation should be able to run this suite unchanged.

## Canonical sources

- Hurl specs: `crude-openapi/src/tests/hurl/*.hurl`
- Test runner: `crude-openapi test --server <base-url>` (implemented in `crude-openapi/src/cli/test.ts`)

## What the suite asserts (today)

The shared suite currently focuses on a required “debug” domain and core navigation behaviors:

- Debug domain exists and is discoverable (`GET /domains/debug`)
- Collections are listable (`GET /domains/debug/collections`) and retrievable
- Elements are listable with pagination (`limit` + `next` cursor) and retrievable
- Missing elements return 404 with Problem Details (`application/problem+json`)
- Organizer is available (`GET /domains/debug/organizer`) and uses `ref` strings like `collection:alphabet`
- Content endpoints support content negotiation (exercises `application/vnd.crude.lines`)

## How to run it

- If a repo provides `mise run test`, prefer that (it should include the shared suite).
- Or run directly against a running server:
  - `crude-openapi test --server http://localhost:<port>`

## Where new tests should live

- If it’s a **protocol-wide requirement**, add it to `crude-openapi/src/tests/hurl/`.
- If it’s an **implementation-specific behavior**, keep it in the repo’s local test area (many repos use `snap/*.hurl`).

