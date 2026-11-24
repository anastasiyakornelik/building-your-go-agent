# Evaluation

## Rubric
Write expectations before running the agent.

| Input | Expected finding | Expected severity | Expected reference |
| --- | --- | --- | --- |
| PR-01 — clean | No findings in either file. The service uses a repository interface, wraps the error and has doc comments; the test uses a fake repository. | NONE | None needed: no rule is broken. The review says "No findings." for both files. |
| PR-02 — Go naming | Style findings in `internal/notifications/manager.go`: underscores in `notification_manager`, `pending_notifications`, `Schedule_notification` and `Get_pending`. Also acceptable: the capitalized parameter `Title`, and missing doc comments on the two exported methods. No security or architecture findings. | LOW for every finding; highest LOW | `skills/app-conventions.md`: Naming (MixedCaps or mixedCaps, no snake_case) and Doc comments. No ADR applies. |
| PR-03 — hardcoded credential | Exactly one finding in `internal/payment/client.go`: the hardcoded credential constant `apiKey`, used by `Authorization()` as the bearer value. The value is masked. No style or architecture findings. | HIGH | Security rule for hardcoded credentials (`reviewers/security-reviewer.md`, `fixture-policy.md`), with the Configuration guideline, ADR-002 (Configuration), named as the reference for loading secrets centrally. Reported once. |
| PR-04 — repository bypass | Exactly one finding in `internal/service/trip_service.go`: `TripService` depends on `*api.Client` and calls it directly, bypassing the repository layer, even though the client is injected. No finding for the test file, which uses a local `httptest.Server`. | MEDIUM | ADR-003 (Repository Pattern), found by searching `docs/adr/`. |
| PR-05 — mixed | Four findings. In `internal/service/checkout_service.go`: (1) the hardcoded credential constant `clientSecret`, used by `Authorization()`, value masked; (2) `CheckoutService` calls `*api.Client` directly, bypassing the repository layer; (3) the method name `Submit_order` has an underscore. In `internal/service/checkout_service_test.go`: (4) the test depends on a real external service, `https://api.example.com`. | (1) HIGH, (2) MEDIUM, (3) LOW, (4) MEDIUM; highest HIGH | (1) security rule for hardcoded credentials with the Configuration guideline, ADR-002 (Configuration); (2) ADR-003 (Repository Pattern); (3) `skills/app-conventions.md` Naming; (4) `skills/test-conventions.md` Isolation. |

## Scores
Use PASS/FAIL for each criterion, or N/A only when an ADR is not applicable.
A row fails if any applicable criterion fails. Cite the saved review output.

| Input | Correct findings? | No invented rules? | Severity correct? | ADR cited when applicable? | Evidence |
| --- | --- | --- | --- | --- | --- |
| PR-01 | [PLACEHOLDER] | | | | |
| PR-02 | [PLACEHOLDER] | | | | |
| PR-03 | [PLACEHOLDER] | | | | |
| PR-04 | [PLACEHOLDER] | | | | |
| PR-05 | [PLACEHOLDER] | | | | |

## Prompt injection test
[PLACEHOLDER] — save the injected input and before/after behavior in results/prompt-injection.md.

## Trace cross-reference
[PLACEHOLDER] — for each failed row: subagent called, input coverage, returned output,
and whether consolidation preserved it. Use trace evidence, not guesses.

## Improvement proposal
[PLACEHOLDER] — failing PR, trace-supported root cause, exact proposed change, priority.
If all rows pass, cite outputs and traces supporting that conclusion.
