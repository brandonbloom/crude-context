# Plugin system (design notes)

This document captures current thinking about Crude’s plugin system. Some sections are aspirational; the OpenAPI spec + conformance suite remain the source of truth for what exists today.

## System Context
A toolkit for building CRUD applications with unified primitives that provide access to collections of elements as web resources. The platform will primarily run in web browsers, with local proxies for accessing local machine resources, and alternative interfaces like CLI tools and virtual filesystems.

## Progressive enhancement and “enriching proxies”

Not every plugin server is expected to implement the full Crude surface area.

- Plugins may implement only a slice of the protocol that matches their capability (for example, a single collection, read-only content, etc.).
- Proxies can provide higher-level behaviors and feature enrichment by composing multiple backing plugins and adapting requests/responses.
- In practice, the Go implementation is expected to be the most feature-rich and is a likely home for these enriching proxies.

See: `multi-language-implementations.md`.

## 1. Plugin Architecture & Communication

**Decision**: Use OpenAPI with REST endpoints for plugin communication.

**Justification**:
- Aligns with the inherent CRUD nature of the application
- Language-agnostic, supporting the goal of plugins in any language
- Well-established standard with good tooling support
- Easier to debug and understand than binary protocols

**Implementation Notes**:
- Standard OpenAPI spec for all plugins
- Health check endpoint as a requirement
- Consider bulk operations and related resources through "meta resources" or patterns like field selection

## 2. Plugin Definition & Discovery

**Decision**: Simple connection string for user-facing plugin definition, with reflective/introspective API for capability discovery.

**Justification**:
- Minimizes user friction - simple to share and install plugins
- Self-describing APIs allow for dynamic discovery of capabilities
- Avoids rigid schema definitions that might limit plugin flexibility

**Implementation Details**:
- Connection strings can specify protocol, host, port, etc.
- JSON configuration for more complex scenarios
- Future marketplace for plugin discovery (itself implemented as a plugin)
- Local plugins defined in config file; remote/hosted server plugins in dynamic database

## 3. Plugin Isolation & Runtime

**Decision (future)**: Container-based isolation for plugins, with supervisor control from main platform.

**Justification**:
- Strong security boundaries between untrusted plugins and core system
- Consistent execution environment regardless of plugin implementation language
- Resource limitation and monitoring capabilities
- Aligns with 12-factor application principles

**Implementation Details**:
- Plugins defined with container type and image:
  ```json
  {
    "type": "container",
    "image": "ghcr.io/myorg/github-plugin:latest"
  }
  ```
- Platform acts as supervisor, starting/stopping plugins as needed
- Version management delegated to container ecosystem

## 4. Authentication & Authorization

**Decision (future)**: Simplified trust model for local plugins; preparation for multi-tenant remote plugins.

**Justification**:
- Initial focus on local plugins reduces authentication complexity
- Container isolation provides security boundaries
- Future-proofing for eventual remote plugin support

**Implementation Details**:
- Local network communication only (localhost/127.0.0.1)
- Optional shared secret in HTTP headers for minimal verification
- Design accommodates future expansion to more robust auth models for remote plugins

## 5. Credential Management

**Decision (future)**: Environment-based secrets injection with encrypted credential store.

**Justification**:
- Follows 12-factor principles for config and backing services
- Separates credentials from plugin code and configuration
- Provides secure storage while maintaining flexibility

**Implementation Details**:
- Platform maintains encrypted credential store
- Secrets referenced by ID in config, values injected at runtime:
  ```json
  {
    "secretMappings": {
      "GITHUB_API_TOKEN": "credential:github-pat-1"
    }
  }
  ```
- CLI for credential management

## 6. Plugin Lifecycle Management

**Decision (future)**: Platform-managed lifecycle with health monitoring and automatic discovery.

**Justification**:
- Ensures resource efficiency by starting/stopping plugins as needed
- Provides reliability through health monitoring
- Simplifies user experience through automatic discovery

**Implementation Details**:
- Plugins register capabilities at startup
- Health endpoint polled periodically
- Restart policies for crashed plugins
- Introspection required for new plugins

## 7. Plugin Interaction

**Decision (future)**: HTTP-based plugin-to-plugin communication with controlled access.

**Justification**:
- Maintains consistent communication model throughout system
- Leverages existing HTTP infrastructure
- Enables controlled interactions while maintaining isolation

**Implementation Details**:
- Plugins can be configured with addresses of other plugins
- Communication flows through standard HTTP like normal systems
- Future consideration: capability-based or ACL-based control over which plugins can interact

## 8. Performance Considerations

**Decision**: Focus on consistently good performance with emphasis on code-to-data approach.

**Justification**:
- Prioritizes responsive UX while avoiding over-optimization
- Chunky calls over chatty calls reduces network overhead
- Sending code to data aligns with modern distributed computing principles

**Implementation Details**:
- Standard CRUD operations with bulk capabilities
- Support for partial responses and field selection
- Designed for interactive web app feel without unnecessary optimization
