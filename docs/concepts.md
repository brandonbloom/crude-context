# Concepts (timeless nouns)

This page is a **non-normative** index of Crude concepts: nouns that should be recognizable across the ecosystem, independent of any specific implementation’s current state.

The **normative** source of truth is the protocol/spec (today: `crude-openapi`), but this list includes concepts that may not be fully represented there yet.

## Core hierarchy

- **Domain**: scoped vocabulary / app-like namespace.
- **Collection**: homogeneous set of elements within a domain.
- **Element**: keyed item within a collection.

## Content model

- **Properties**: structured attributes associated with an element (often queryable/selectable).
- **Content**: native (not-necessarily JSON) representation of a resource.
- **Format**: a content representation (often identified by media type).
- **Converter**: transforms content between formats.

## Navigation

- **Organizer**: hierarchical navigation model (sidebar, filesystem-like browsing).
- **Navigation ref**: a ref string intended for navigation (organizer entries, search results, etc.).

## Reflection / meta

- **Universe** (`_universe`): a system-reserved view of “what this server knows” (domains, collections, search across domains, etc.).
- **Crude domain** (`crude`): an installable reflective domain exposing Crude’s own internals/configuration (admin-oriented by default).
- **System-reserved names** (`_...`): underscore-prefixed keys/subresources reserved by the Crude system to avoid collisions with user/backing keyspaces.
- **Lifted resources** (`_collection`, `_element`, …): a pattern for “lifting” convenient/static endpoints into the generic collection/element/content shapes for polymorphic tooling.

## Protocol patterns

- **Page** / **Cursor**: paginated listing model.
- **Filter**: a predicate restricting element listings.
- **Search**: ranked, heterogeneous results.
- **Problem Details**: standardized error payloads.

## Extensibility

- **Plugin**: independently-developed server capability exposed via the protocol.
- **Enriching proxy**: composes/backfills capabilities across plugins.
- **Capability discovery**: explicit mechanism to preflight supported features (in addition to runtime 404/405/unsupported).

See also:

- Dense map: `concept-map.md`
- Glossary: `glossary.md`
- Meta-circular reflection: `design/meta-circularity.md`

