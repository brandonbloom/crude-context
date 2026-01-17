# Ports, URIs, and env vars (conventions)

This is a shared registry of **conventions** used across Crude repos to reduce friction. Treat these as defaults; repo-local docs may override them.

## Canonical sources

- CLI usage + `CRUDE_URI`: `crude-cli/README.md`, `crude-docs/docs/cli/usage.md`
- Web UI expectations: `crude-web/README.md`, `crude-web/mise.toml`
- Server/test scripts: `crude-go/mise.toml`, `crude-go/test.sh`, `crude-swift/mise.toml`

## URIs

- `CRUDE_URI`: default server base URI for clients (notably the CLI).
- Supported schemes include:
  - `http://` and `https://`
  - `crude:embedded` (where supported by a given build)

## Common local URLs (observed)

- `http://localhost:7070/`
  - Common “default” server URL used by clients and generated specs.
- `http://localhost:7071/`
  - Common local dev/test port used by the Go server (`crude-go/test.sh` and Hurl readiness checks).
- `http://localhost:8080/`
  - Common local dev port used by the Swift server (and its shared-suite invocation).
- `http://localhost:5173/` (web dev server)
  - In dev, the web UI proxies API requests under `http://localhost:5173/api/`.

## Guidance

- Prefer using `CRUDE_URI` (or repo-specific env) instead of hardcoding server URLs in code.
- When adding a new server implementation, ensure it can run the shared conformance suite and document the chosen default port in that repo.

