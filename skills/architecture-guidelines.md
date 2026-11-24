## Rules

These rules summarize the four team ADRs in `docs/adr/`.

Layered architecture (ADR-001) - `[MEDIUM]`:
- Handlers and `cmd/` entrypoints parse input and format output.
- Services contain the business logic.
- Entrypoints do not query persistence directly.

Configuration (ADR-002) - `[LOW]`:
- Runtime configuration and secrets are loaded centrally in `internal/config` or
  the composition root and injected into constructors.
- Services do not call `os.Getenv` ad hoc.
- Ordinary algorithm constants are allowed.
- A hardcoded credential is a `[HIGH]` security finding that belongs to the
  security reviewer. Do not report it a second time as an architecture finding.

Repository pattern (ADR-003) - `[MEDIUM]`:
- Services obtain data through small repository interfaces, normally declared
  near the consumer.
- Concrete adapters in `internal/repository` may depend on `internal/api` or
  `database/sql`.
- Services must not import or call API clients or SQL directly.
- Repository bypass is `[MEDIUM]`, even when the client is injected. Injecting a
  client satisfies dependency injection but still bypasses the repository.
- Tests may wire adapters to `httptest` servers.

Dependency injection (ADR-004) - `[MEDIUM]`:
- Dependencies are passed through `NewXxx` constructors or function parameters.
- Small interfaces are used where consumers need substitution.
- No global mutable service instances, global clients or `ServiceLocator`
  registries.
- Tests provide fakes or local adapters.

Severity:
- `[HIGH]` - only a demonstrably serious violation.
- `[MEDIUM]` - repository bypass, a layering violation, global mutable
  dependencies or a service locator.
- `[LOW]` - a minor issue, such as an ad hoc `os.Getenv` call in a service.
