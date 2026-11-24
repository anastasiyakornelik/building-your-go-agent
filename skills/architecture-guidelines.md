## Rules

These are this team's decisions, not general Go rules. They summarize the four
ADRs in `docs/adr/`.

### Layered architecture

- Handlers and `cmd/` entry points parse input and format output.
- Services contain business logic.
- Entry points do not query storage directly.

### Configuration

- Runtime configuration and secrets are loaded centrally, in `internal/config` or
  the composition root, and passed through constructors.
- Services do not call `os.Getenv` and do not contain credentials.
- Ordinary algorithm constants are allowed.

### Repository pattern

- Services get data through small repository interfaces, normally declared near
  the consumer.
- Only adapters in `internal/repository` may use `internal/api` or `database/sql`.
- Services do not import or call API clients or SQL directly.

### Dependency injection

- Dependencies come through `NewXxx` constructors or function parameters.
- No global services, global clients or service locators.
- Tests provide fakes or local adapters.

### Clarifications

- Injecting a concrete API client into a service follows dependency injection but
  still bypasses the repository layer. Report it as a Repository pattern
  violation.
- Tests may connect adapters to a local `httptest.Server`.
- A hardcoded credential is mainly a security finding. It is reported once, as
  `[HIGH]` security with a reference to the Configuration guideline, and not
  again as a separate architecture finding.

### Severity

- `[MEDIUM]` - a normal violation of any of the four decisions, including a
  repository bypass with an injected client.
- `[HIGH]` - only when the changed code shows an additional high-impact
  consequence.
