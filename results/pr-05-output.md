# PR-05 — final-configuration run

Tool/model/version, date, repository/PR/head and trace ID: Claude Code 2.1.331; claude-opus-5-5 main agent with claude-haiku-5-5 (style-reviewer) and claude-sonnet-5-5 (security-reviewer, architecture-reviewer); langsmith-tracing plugin 0.7.0; 2026-11-24; nebius-academy-templates/go-pr-review-practice PR #5, github mode; head SHA feedb3b27ee87b4823dc1ae441ae58f05c33a36c; LangSmith trace ID 01a20e8d-a7f1-7b7b-9dd9-7e72e82da406 (project go-pr-review-agent, metadata pr_number 5)

## Complete recorded review and declined publication

Conversation exported from Claude Code with /export. I answered `no` to the confirmation question.

````text
▗ ▗   ▖ ▖  Claude Code v2.1.331
           Opus 5.5 · Claude Team
 ▘▘   ▝▝   ~/inap-tests/building-your-go-agent/building-your-go-agent


❯ /review-pr 5                                                                  

  Called github 3 times, ran 1 shell command (ctrl+o to expand)

⏺ style-reviewer(Style review PR-05)
  ⎿  Backgrounded agent (↓ to manage · ctrl+o to expand)

⏺ I've fetched PR #5 (get, get_diff, get_files) and started the style reviewer.
  I'll wait for its result before starting the security and architecture
  reviewers.

✻ Worked for 21s · done 7:15 PM

› Message from style-reviewer
  Style review, PR #5 (nebius-academy-templates/go-pr-review-practice), head
  feedb3b27ee87b4823dc1ae441ae58f05c33a36c, full scope. Both changed files are
  present in the diff, so there is no coverage gap.

  internal/service/checkout_service.go
  - [LOW] internal/service/checkout_service.go:22 - `func (s *CheckoutService)
  Submit_order(ctx context.Context) (int, error)`. The exported method name
  contains an underscore, so it does not use MixedCaps. The doc comment on line
  21 repeats the same name. Suggested change: rename to `SubmitOrder` and update
  the doc comment to start with `SubmitOrder`; the call site in
  internal/service/checkout_service_test.go:21 must change with it. Rule:
  app-conventions.md, Naming (no underscores in exported identifiers,
  MixedCaps).

  internal/service/checkout_service_test.go
  - [MEDIUM] internal/service/checkout_service_test.go:19 - `client :=
  api.NewClient(&http.Client{Timeout: 2 * time.Second},
  "https://api.example.com")`, gated only by the `RUN_LIVE_TESTS` check on lines
  16-17. The test depends on a real external host and on live data (`count !=
  1` on line 25), which is shared state. Suggested change: replace the live host
  with an `httptest.Server` that returns a fixed rides payload, and assert the
  count against that fixture. Do not remove the opt-in gate just to satisfy
  this; the external-service test in PR-05 must stay disabled per AGENTS.md.
  Rule: test-conventions.md, Isolation (no real external services; use
  httptest.Server or in-package fakes).

  Checked and clean in the test file: the test name describes behavior, `err` is
  checked with `t.Fatalf` before the result is used, the assertion is on the
  returned count, and no test-order or package-level state is involved. Checked
  and clean in the app file: the error from `RecentRides` is wrapped with `%w`
  and returned, `ctx` is the first parameter and is passed on, all exported
  declarations have doc comments that start with their names, and the formatting
  looks gofmt-compliant.

  Deferred to the security reviewer (not a style finding, value not reproduced):
  checkout_service.go:10 contains a hardcoded credential-like constant
  `clientSecret`, and line 25 of the same file builds a bearer header from it.

  Summary: 2 style findings (1 LOW, 1 MEDIUM), plus the deferred security note
  above.

⏺ security-reviewer(Security review PR-05)
  ⎿  Backgrounded agent (↓ to manage · ctrl+o to expand)

⏺ Agent "Style review PR-05" finished · 17s

⏺ The style reviewer finished with 2 findings: one LOW and one MEDIUM. The
  security reviewer is running now, and I'll start the architecture reviewer
  once it returns.

✻ Baked for 12s · done 7:15 PM

› Message from security-reviewer
  ### internal/service/checkout_service.go
  - [HIGH] internal/service/checkout_service.go:10 - `const clientSecret =
  "<redacted>"` is a hardcoded client secret in a package-level constant.
  Impact: the credential is committed to source control and compiled into every
  binary, so anyone with repo or artifact access can read it, and rotating it
  requires a code change and redeploy. Remediation: remove the literal; secrets
  are loaded from central configuration and injected into the service (for
  example through the constructor). Rule: hardcoded API keys, tokens, passwords,
  client secrets and other credentials.
  - [HIGH] internal/service/checkout_service.go:18 - `Authorization()` returns
  `"Bearer " + clientSecret`, so the hardcoded secret is used to build and
  expose an Authorization header value. Impact: the same committed credential is
  used and handed to callers as a bearer token. Remediation: build the
  Authorization value from a secret that is injected from central configuration,
  not from a source constant. Rule: hardcoded credentials (placement and use
  per fixture-policy.md). Note: this is the same credential as the line 10
  finding. If the orchestrator treats a hardcoded credential as one finding,
  merge lines 10 and 18 into a single finding anchored at line 10.

  ### internal/service/checkout_service_test.go
  No security findings in this file. The live-service test is skipped unless
  `RUN_LIVE_TESTS` is `"1"`. I did not enable `RUN_LIVE_TESTS`. The HTTP client
  uses an `https://` base URL with a default TLS configuration and no
  `InsecureSkipVerify`.

  Scope: both changed files were fully covered by the supplied diff. There are
  no gaps.

⏺ architecture-reviewer(Architecture review PR-05)
  ⎿  Backgrounded agent (↓ to manage · ctrl+o to expand)

⏺ Agent "Security review PR-05" finished · 6s

⏺ The security reviewer finished and flagged a hardcoded client secret at
  checkout_service.go:10, which Authorization() also uses. The architecture
  reviewer is running now. I'll merge all three results when it returns.

✻ Worked for 12s · done 7:16 PM

› Message from architecture-reviewer
  ### internal/service/checkout_service.go
  - [MEDIUM] internal/service/checkout_service.go:6 and :13 and :22 - The
  service imports `internal/api`, stores a concrete `*api.Client` in
  `CheckoutService`, and calls `s.client.RecentRides(ctx)` directly in
  `Submit_order`. This is a repository-layer bypass. Injecting the client
  through `NewCheckoutService` satisfies dependency injection, but the service
  still depends on an API client and not on a repository interface. Guideline:
  Repository pattern - violates ADR-003: Repository Pattern. Suggested change:
  declare a small interface near the consumer (for example `RideRepository` with
  `RecentRides(ctx)`), implement it in an adapter in `internal/repository` that
  wraps `api.Client`, and have `NewCheckoutService` take the interface.

  - [MEDIUM] internal/service/checkout_service.go:15-16 - `Authorization()`
  builds a credential header inside the service from a package-level constant
  (line 10). Credentials and runtime configuration belong outside services. This
  is the architecture side only: the hardcoded credential itself is reported
  once by the security reviewer as [HIGH], so it is not repeated here.
  Guideline: Configuration - violates ADR-002: Configuration (services must not
  embed credentials). Suggested change: load the secret centrally in
  `internal/config` or the composition root, and pass it, or a ready-made
  authorizer, into `NewCheckoutService`. Drop the package-level constant.

  ### internal/service/checkout_service_test.go
  - [MEDIUM] internal/service/checkout_service_test.go:19-20 - The test builds a
  real `api.NewClient` against an external URL and passes it to
  `NewCheckoutService`. The service has no seam for a fake. The tests should use
  a fake repository, or an adapter wired to a local `httptest.Server`. This
  follows from the finding above, since the service depends on a concrete
  client. The test is skipped unless `RUN_LIVE_TESTS=1`, and it was not enabled
  during this review. Guideline: Dependency injection (tests provide fakes or
  local adapters) - violates ADR-004: Dependency Injection. Suggested change:
  after introducing the repository interface, test the service with an in-memory
  fake. Test the adapter in `internal/repository` against `httptest.Server`.

  Gap: none. The diff covers both changed files in the scope.

  ADRs read: ADR-002: Configuration, ADR-003: Repository Pattern, ADR-004:
  Dependency Injection. Each was confirmed by Grep over `docs/adr/`. The ADR-003
  and ADR-004 matches were on keyword lines, and the ADR-002 line was read as
  well.

  Not reported, to avoid duplicates: the hardcoded credential (security
  reviewer), and the `Submit_order` underscore name (style reviewer).

⏺ Bash(tail -c1 review_history.md | xxd | head -1 && echo "2026-11-24 | PR-05 |
      Hardcoded client secret constant used as bearer token by Authorization()
      (internal/serv…)
  ⎿  00000000: 0a                                       .
     2026-11-24 | PR-03 | Hardcoded API key in payment client constant (int
     ernal/payment/client.go:3) | HIGH
     … +6 lines (ctrl+o to expand)
  ⎿  Updated review_history.md +1
      14  2026-11-24 | PR-02 | Underscores in type, field and method name
          s (internal/notifications/manager.go:3) | LOW
      15  2026-11-24 | PR-03 | Hardcoded API key in payment client consta
          nt (internal/payment/client.go:3) | HIGH
      16  2026-11-24 | PR-04 | TripService depends on api.Client directly
          , bypassing repository layer (ADR-003, internal/service/trip_se
          rvice.go:12) | MEDIUM
      17 +2026-11-24 | PR-05 | Hardcoded client secret constant used as b
         +earer token by Authorization() (internal/service/checkout_servi
         +ce.go:10, 19) | HIGH                                           

⏺ Agent "Architecture review PR-05" finished · 9s

⏺ All three reviewers ran one after another, in a single pass. The worst problem
  in this PR is a client secret written directly into the code. I masked the
  secret's value in the diff I gave the reviewers, and this review doesn't
  repeat it.

  internal/service/checkout_service.go

  - [HIGH] (security) Line 10, also used at line 19: the constant clientSecret
    holds a credential written directly into the code (<redacted>), and
    Authorization() uses it to build the bearer token it returns. This also
    violates ADR-002 (Configuration). Remove the constant, load the secret from
    central configuration, and pass it in through NewCheckoutService.
  - [MEDIUM] (architecture) Lines 7, 13 and 23: CheckoutService stores a
    concrete *api.Client and calls s.client.RecentRides(ctx) directly, so the
    service skips the repository layer. This violates ADR-003 (Repository
    Pattern). Add a RideRepository interface with RecentRides(ctx) and an
    adapter for it in internal/repository, and have NewCheckoutService take that
    interface.
  - [LOW] (style) Lines 21–22: the exported method Submit_order and its doc
    comment use an underscore, which breaks the MixedCaps naming rule. Rename it
    to SubmitOrder, and update the doc comment and the calls in the test file.

  internal/service/checkout_service_test.go

  - [MEDIUM] (style, architecture) Lines 19–20: the test builds a real
    api.Client against an external URL and expects live data (count != 1),
    instead of using a fake or a local test server. This violates ADR-004
    (Dependency Injection). Once the repository interface exists, test the
    service with an in-memory fake, and test the adapter against an
    httptest.Server. The RUN_LIVE_TESTS check stays in place and the test stays
    disabled.

  Summary

  HIGH: 1, MEDIUM: 2, LOW: 1. Highest severity: HIGH.

  When I merged the results:
  - The security reviewer reported the secret twice (lines 10 and 18) and the
    architecture reviewer flagged it under ADR-002. I combined these into the
    single HIGH finding.
  - Both the style and architecture reviewers flagged the live test, so it
    appears once.
  - I corrected three line numbers against the diff: the Authorization()
    function is on line 19, not 18 or 15–16; the internal/api import is on line
    7, not 6; and the RecentRides call is on line 23, not 22.

  I added one line for PR-05 to the end of review_history.md. Earlier sessions
  left 4 duplicate lines (PR-01 to PR-04) in the uncommitted part of that file,
  and I left them as they are.

  Target: PR #5, nebius-academy-templates/go-pr-review-practice, head SHA
  feedb3b27ee87b4823dc1ae441ae58f05c33a36c.

  Post this review? Reply yes or post to confirm.

✻ Crunched for 1m 32s · done 7:16 PM

❯ no                                                                            

⏺ Nothing was posted to PR #5. The review stays in this session only, and I
  didn't change review_history.md again.

✻ Crunched for 1s · done 7:20 PM
````
