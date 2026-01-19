# Meta-circularity and reflection (design note)

This note describes a central Crude idea: **core concepts can be represented in terms of themselves**, so the same leverage given to plugins is available for Crude itself.

This is **non-normative**; the protocol/spec is authoritative.

## The meta-circular goal

Crude’s primary abstraction is:

> Everything is made of **collections of elements**.

Meta-circularity applies that same abstraction to Crude’s own “system concepts” (domains, collections, formats, plugins, configuration, etc.):

- If a thing is important to reason about, it should be possible to expose it as a **collection**.
- If a thing can be addressed, it should have a **key** and be representable as an **element**.
- If a thing has a natural representation, it should be retrievable as **content** in one or more **formats**.

This supports polymorphic tooling:

- UIs can browse “system things” using the same organizer/collection/element rendering they use for user data.
- CLIs can list/search/filter “system things” using the same commands.
- Proxies can introspect “system things” to adapt behavior dynamically.

## Universe vs crude

Crude anticipates (at least) two reflective layers:

- **Universe** (`_universe`, system-reserved):
  - Represents the server’s *effective view of the world*: what domains/collections are available given the server’s role, configuration, and loaded extensions.
  - Exists to make “universal” / root-scoped behavior easy to access via a domain-shaped surface.

- **Crude domain** (`crude`, installable):
  - Represents Crude’s *own internals* as data.
  - Admin-oriented by default (security policy determines what is visible).
  - Not system-reserved so operators can choose whether/how to expose it (though a future `_crude` system domain may exist).

## Reserved names (`_...`)

Underscore-prefixed keys are reserved for system use to avoid collisions with:

- user-defined keys inside collections
- user/admin domain names
- future protocol extensions

This applies to both **keys** (e.g. domains) and potentially **subresources** or other addressable names.

## “Lifting” static resources into generic shapes

Some endpoints are “convenience-shaped” (Rails-ish): they are easy to call directly, but don’t automatically participate in generic collection tooling.

To preserve generic/polymorphic access, consider a convention of **lifted subresources**:

- `.../_collection`: treat a static listing endpoint as a generic collection of elements.
- `.../_element`: treat a static “get” endpoint as a generic element resource.

Examples (illustrative, not committed):

- `/domains` is convenient for “list domains”, while `/domains/_collection` could expose domains as an element collection.
- `/domains/{domain}` is convenient for “get domain”, while `/domains/{domain}/_element` could expose the domain as a generic element.

The intent is:

- If you know what you’re doing, use the convenient/static endpoints.
- If you’re building a generic client (UI/CLI/proxy), use lifted endpoints so the same code paths work for many concepts.

## Navigation refs

Organizer entries and search results should be representable as **navigation refs**: compact strings that can be resolved in context to a navigable target (domain, collection, element, help page, external URL, etc.).

Key properties of navigation refs:

- Opaque by default: clients may interpret known schemes, but should not guess.
- Context-sensitive: resolution depends on the server/domain and the consumer (web UI vs CLI vs proxy).
- Designed for ergonomics: easy to read/type/embed in URLs/CLI args.

## Meta-spec and routing DSL (future direction)

If collections/elements become the universal abstraction, then “resource routing” can be expressed *in terms of* those abstractions:

- A collection is analogous to a controller: it defines operations over a set of elements/content.
- A routing DSL can map those operations into RESTful paths consistently (including pagination, filtering, auth, etc.).

This likely feeds back into the `crude-openapi` meta-spec, but introduces a meta-circular design problem:

- the spec describes concepts,
- concepts describe a DSL for describing specs.

Treat this as an explicit design space to untangle rather than an accidental complexity.

