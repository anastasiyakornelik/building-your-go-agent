# Practice 3 — actual session evidence

Tool/model/version and date: Claude Code v2.1.331, Opus 5.5, 2026-11-24
Target, mode and head SHA when live: nebius-academy-templates/go-pr-review-practice, github mode; PR #2 application files (head SHA 6a01fd4b95c55923ff33993c7a48a940f9e9f8f8), PR #4 internal/service/trip_service_test.go (head SHA baa1692dbb1ee9e1c1cb91e2428aebbe38ec1a95), PR #1 via /review-pr 1 (head SHA 2d551765be07e61e70f70245c9f7be2943f901f4)

## Recorded conversation and tool events

### Test 1 - selective loading, application files of PR #2

#### Conversation (Claude Code /export)

```text
▗ ▗   ▖ ▖  Claude Code v2.1.331
           Opus 5.5 · Claude Team
 ▘▘   ▝▝   ~/inap-tests/building-your-go-agent/building-your-go-agent


❯ Review only the application files in PR #2 from                               
  https://github.com/nebius-academy-templates/go-pr-review-practice for style   
  issues.                                                                       

  Listed 2 directories, called github 2 times (ctrl+o to expand)

⏺ Skill(app-conventions)
  ⎿  Loaded skill

⏺ I reviewed PR #2 in nebius-academy-templates/go-pr-review-practice for style
  only. It changes one file, internal/notifications/manager.go, which is an
  application file, so I loaded app-conventions for it. The PR has no _test.go
  files, so I didn't load test-conventions.

  internal/notifications/manager.go

  - [LOW] Line 3 (type notification_manager): The type name has an underscore,
    which breaks Go's mixedCaps naming. Rename it to notificationManager.
  - [LOW] Line 4 (pending_notifications): The field name has an underscore,
    which breaks Go's mixedCaps naming. Rename it to pendingNotifications.
  - [LOW] Line 7 (Schedule_notification): The method name has an underscore,
    which breaks Go's MixedCaps naming. Rename it to ScheduleNotification.
  - [LOW] Line 7 (parameter Title): The parameter name starts with a capital
    letter, but unexported names use mixedCaps. Rename it to title.
  - [LOW] Line 7 (Schedule_notification): This exported method has no doc
    comment. Add a comment that starts with the method's name.
  - [LOW] Line 11 (Get_pending): The method name has an underscore, which breaks
    Go's MixedCaps naming. Rename it to GetPending or the more idiomatic
    Pending.
  - [LOW] Line 11 (Get_pending): This exported method has no doc comment. Add a
    comment that starts with the method's name.

  Summary

  HIGH: 0, MEDIUM: 0, LOW: 7. Highest severity: LOW.

  Target: PR #2, nebius-academy-templates/go-pr-review-practice, head SHA
  6a01fd4b95c55923ff33993c7a48a940f9e9f8f8.

  Post this review? Reply yes or post to confirm.

✻ Worked for 20s · done 2:06 PM

❯ /export                                                                       
  ⎿  Conversation copied to clipboard
```

#### Tool events (from the Claude Code session log)

```text
TOOL CALL: Bash {"command": "cat review-target.json; echo ---; ls; echo ---; cat skills/review-pr.md; echo ---; cat skills/app-conventions.md; echo ---; ls .claude/skills .claude/agents 2>/dev/null", "description": "Read review target config and skill files"}

TOOL CALL: ToolSearch {"query": "select:mcp__github__pull_request_read", "max_results": 1}

TOOL RESULT:


TOOL RESULT:
{
  "mode": "github",
  "owner": "nebius-academy-templates",
  "repo": "go-pr-review-practice",
  "pr_numbers": {
    "1": 1,
    "2": 2,
    "3": 3,
    "4": 4,
    "5": 5
  },
  "tool": "claude",
  "_tool_comment": "Choose before Practice 1: codex (Codex CLI or desktop app) or claude (Claude Code)."
}
---
AGENTS.md
CLAUDE.md
README.md
docs
fixture-policy.md
practice-description.md
results
review-target.json
review_history.md
reviewers
samples
setup
skills
---
## Steps

1. Resolve the target. Take the PR number from the argument. If it is missing or
   is not one of the numbers in `review-target.json`, stop and ask for it. Read
   `review-target.json` for the mode, owner, repository and PR number mapping.

2. Fetch the change.
   - In `github` mode, call the GitHub MCP tool `pull_request_read` with `get` for
     the title and head SHA, then with `get_diff` for the diff. Call `get_files`
     when you need the list of changed files.
   - In `local` mode, read the matching file under `samples/`. There is no head
     SHA in local mode.
   - Never substitute a local sample for a live PR. If the GitHub MCP server is
     not connected, stop and say so.

3. Route the changed files by type and load only the conventions that are needed.
   - A `.go` file whose basename does not end in `_test.go`: load `app-conventions`.
   - A file whose basename ends in `_test.go`: load `test-conventions`.
   - Any other file: load no convention skill.
   - If the request names a file scope, review only the files in that scope.
   - Say which convention skills you loaded and for which files.

4. Review each file in scope. Apply the review criteria in `AGENTS.md`, including its security rules, the
   conventions loaded for that file, and `fixture-policy.md`. Report only issues
   you can point to in the diff, with the file and the line or hunk. Do not apply
   application conventions to test files or test conventions to application files.

5. Check the findings once more against the diff. Drop any finding you cannot
   point to, and merge duplicates. Make at most two review passes in total.

6. Consolidate one review in the output format from `AGENTS.md`: a section for
   each file in scope, `No findings.` for a clean file, and a closing `## Summary`
   with the counts by severity and the highest severity.

7. Present the review. Name the target: the PR number, the repository and the
   head SHA, or the local file. End with
   "Post this review? Reply yes or post to confirm." Do not post.

8. Follow the publish gate in `AGENTS.md`. Post only after an exact `yes` or
   `post`, and only with a write tool that the connection provides. Never approve,
   merge or close the PR.

Do the whole review in the main agent. Do not delegate to subagents in this
practice.
---
## Rules

Scope: apply these rules to changed `.go` files whose basename does not end in
`_test.go`. Do not apply them to test files; those follow `test-conventions`.

Naming - `[LOW]`:
- Exported identifiers use `MixedCaps`; unexported identifiers use `mixedCaps`.
  No underscores and no `ALL_CAPS` constants.
- Initialisms keep one case: `ID`, `URL`, `HTTP`, `API`, `JSON`, `SQL`
  (`userID`, `ParseURL`, not `userId`, `ParseUrl`).
- Package names are short, lowercase, and have no underscores.

Doc comments - `[LOW]`:
- Every exported type, function, method, constant and variable has a doc comment.
- The comment is a full sentence that starts with the name it documents.
- Unexported identifiers do not need a doc comment.

Formatting - `[LOW]`:
- Code matches `gofmt` output: tabs for indentation, gofmt spacing and alignment.

Errors - `[MEDIUM]` for a discarded or unchecked error, `[LOW]` for wrapping and error string style:
- Never discard an error: no `_ = f()` and no `value, _ := f()`.
- Check the error right after the call and return early.
- When adding context to an error, wrap it with `fmt.Errorf("...: %w", err)` so callers can use
  `errors.Is` and `errors.As`. Do not use `%v` or `%s` 
[output truncated]

TOOL RESULT:


TOOL RESULT:
{
  "mode": "github",
  "owner": "nebius-academy-templates",
  "repo": "go-pr-review-practice",
  "pr_numbers": {
    "1": 1,
    "2": 2,
    "3": 3,
    "4": 4,
    "5": 5
  },
  "tool": "claude",
  "_tool_comment": "Choose before Practice 1: codex (Codex CLI or desktop app) or claude (Claude Code)."
}
---
AGENTS.md
CLAUDE.md
README.md
docs
fixture-policy.md
practice-description.md
results
review-target.json
review_history.md
reviewers
samples
setup
skills
---
## Steps

1. Resolve the target. Take the PR number from the argument. If it is missing or
   is not one of the numbers in `review-target.json`, stop and ask for it. Read
   `review-target.json` for the mode, owner, repository and PR number mapping.

2. Fetch the change.
   - In `github` mode, call the GitHub MCP tool `pull_request_read` with `get` for
     the title and head SHA, then with `get_diff` for the diff. Call `get_files`
     when you need the list of changed files.
   - In `local` mode, read the matching file under `samples/`. There is no head
     SHA in local mode.
   - Never substitute a local sample for a live PR. If the GitHub MCP server is
     not connected, stop and say so.

3. Route the changed files by type and load only the conventions that are needed.
   - A `.go` file whose basename does not end in `_test.go`: load `app-conventions`.
   - A file whose basename ends in `_test.go`: load `test-conventions`.
   - Any other file: load no convention skill.
   - If the request names a file scope, review only the files in that scope.
   - Say which convention skills you loaded and for which files.

4. Review each file in scope. Apply the review criteria in `AGENTS.md`, including its security rules, the
   conventions loaded for that file, and `fixture-policy.md`. Report only issues
   you can point to in the diff, with the file and the line or hunk. Do not apply
   application conventions to test files or test conventions to application files.

5. Check the findings once more against the diff. Drop any finding you cannot
   point to, and merge duplicates. Make at most two review passes in total.

6. Consolidate one review in the output format from `AGENTS.md`: a section for
   each file in scope, `No findings.` for a clean file, and a closing `## Summary`
   with the counts by severity and the highest severity.

7. Present the review. Name the target: the PR number, the repository and the
   head SHA, or the local file. End with
   "Post this review? Reply yes or post to confirm." Do not post.

8. Follow the publish gate in `AGENTS.md`. Post only after an exact `yes` or
   `post`, and only with a write tool that the connection provides. Never approve,
   merge or close the PR.

Do the whole review in the main agent. Do not delegate to subagents in this
practice.
---
## Rules

Scope: apply these rules to changed `.go` files whose basename does not end in
`_test.go`. Do not apply them to test files; those follow `test-conventions`.

Naming - `[LOW]`:
- Exported identifiers use `MixedCaps`; unexported identifiers use `mixedCaps`.
  No underscores and no `ALL_CAPS` constants.
- Initialisms keep one case: `ID`, `URL`, `HTTP`, `API`, `JSON`, `SQL`
  (`userID`, `ParseURL`, not `userId`, `ParseUrl`).
- Package names are short, lowercase, and have no underscores.

Doc comments - `[LOW]`:
- Every exported type, function, method, constant and variable has a doc comment.
- The comment is a full sentence that starts with the name it documents.
- Unexported identifiers do not need a doc comment.

Formatting - `[LOW]`:
- Code matches `gofmt` output: tabs for indentation, gofmt spacing and alignment.

Errors - `[MEDIUM]` for a discarded or unchecked error, `[LOW]` for wrapping and error string style:
- Never discard an error: no `_ = f()` and no `value, _ := f()`.
- Check the error right after the call and return early.
- When adding context to an error, wrap it with `fmt.Errorf("...: %w", err)` so callers can use
  `errors.Is` and `errors.As`. Do not use `%v` or `%s` 
[output truncated]

TOOL CALL: mcp__github__pull_request_read {"method": "get", "owner": "nebius-academy-templates", "repo": "go-pr-review-practice", "pullNumber": 2}

TOOL CALL: mcp__github__pull_request_read {"method": "get_diff", "owner": "nebius-academy-templates", "repo": "go-pr-review-practice", "pullNumber": 2}

TOOL CALL: Bash {"command": "ls -R .claude/skills; cat .claude/skills/review-pr/*.md | head -80; echo ---; cat fixture-policy.md", "description": "Read installed skill files and fixture policy"}

TOOL RESULT:
app-conventions
architecture-guidelines
review-pr
test-conventions

.claude/skills/app-conventions:
SKILL.md

.claude/skills/architecture-guidelines:
SKILL.md

.claude/skills/review-pr:
SKILL.md

.claude/skills/test-conventions:
SKILL.md
---
name: review-pr
description: "Run a complete Go PR review of a course PR. Explicit invocation only, via /review-pr <PR number> (e.g. /review-pr 3)."
disable-model-invocation: true
---

Read `skills/review-pr.md` relative to the repository root and follow its steps in order.
The shared workflow determines whether this practice uses subagents.

Before delegation is introduced (Practice 3), review in the main agent. Load
`app-conventions` only for changed application `.go` files and `test-conventions`
only for changed `_test.go` files, respecting the requested scope.

When the shared workflow calls for delegation (Practice 4 onward), use the Agent tool
with the named `style-reviewer`, `security-reviewer`, and `architecture-reviewer`
subagents. Run them one at a time and wait for each result before starting the next.
Pass all three the same target, head SHA, repository root, review scope and complete
diff. Let the subagents read their shared reviewer prompts and convention files.
If a named agent is unavailable, report the missing agent instead of simulating it.

PR number supplied by Claude Code: $ARGUMENTS.
---
# Fixture policy
These changes are intentionally flawed teaching inputs, never production code.
All credential strings are synthetic and cannot authenticate. Still report their placement
and use as HIGH in this controlled exercise. In real reviews, distinguish fixtures from
actual credentials. Do not reproduce a credential value in a review or trace.
PR-05's external-service test is skipped unless RUN_LIVE_TESTS=1; keep it disabled.
Every PR is independently based on base/, not on the preceding PR.

TOOL RESULT:
{"additions":13,"base":{"ref":"main","repo":{"full_name":"nebius-academy-templates/go-pr-review-practice"},"sha":"cb7a691656d4881e5d045a2308dbaf0d5de52c69"},"body":"## Summary\n\nAdd a lightweight notification manager that stores pending notification titles and returns them to callers.\n\n## Changes\n\n- Added a notification manager type.\n- Added an operation for scheduling notifications.\n- Added an operation for retrieving pending notifications.\n- Returned a copy of the pending notification list to prevent external mutation.\n\n## Testing\n\n- `go test ./...`\n- `go vet ./...`","changed_files":1,"commits":2,"created_at":"2026-10-20T13:48:56Z","draft":false,"head":{"ref":"pr-02","repo":{"full_name":"nebius-academy-templates/go-pr-review-practice"},"sha":"6a01fd4b95c55923ff33993c7a48a940f9e9f8f8"},"html_url":"https://github.com/nebius-academy-templates/go-pr-review-practice/pull/2","mergeable_state":"clean","merged":false,"number":2,"state":"open","title":"Add notification scheduling manager","updated_at":"2026-10-20T13:48:56Z","user":{"avatar_url":"https://avatars.githubusercontent.com/u/172391715?v=4","id":172391715,"login":"ispaniuktripleten","profile_url":"https://github.com/ispaniuktripleten"}}

TOOL RESULT:
{"diff":"diff --git a/internal/notifications/manager.go b/internal/notifications/manager.go\nnew file mode 100644\nindex 0000000..eab40ac\n--- /dev/null\n+++ b/internal/notifications/manager.go\n@@ -0,0 +1,13 @@\n+package notifications\n+\n+type notification_manager struct {\n+\tpending_notifications []string\n+}\n+\n+func (m *notification_manager) Schedule_notification(Title string) {\n+\tm.pending_notifications = append(m.pending_notifications, Title)\n+}\n+\n+func (m *notification_manager) Get_pending() []string {\n+\treturn append([]string(nil), m.pending_notifications...)\n+}\n"}

TOOL CALL: Skill {"skill": "app-conventions"}

TOOL RESULT:
Launching skill: app-conventions

TOOL CALL: Skill {"skill": "app-conventions"}

TOOL RESULT:
Launching skill: app-conventions
```

### Test 2 - selective loading, test file of PR #4

#### Conversation (Claude Code /export)

```text
▗ ▗   ▖ ▖  Claude Code v2.1.331
           Opus 5.5 · Claude Team
 ▘▘   ▝▝   ~/inap-tests/building-your-go-agent/building-your-go-agent


❯ Review only internal/service/trip_service_test.go in PR #4 from               
  https://github.com/nebius-academy-templates/go-pr-review-practice for style   
  issues.                                                                       

  Listed 1 directory (ctrl+o to expand)

⏺ Skill(test-conventions)
  ⎿  Loaded skill

  Read 1 file, called github 2 times (ctrl+o to expand)

⏺ I checked the test file against the team's test conventions and the style
  rules in AGENTS.md. As you asked, I didn't review
  internal/service/trip_service.go, the other file in this PR.

  internal/service/trip_service_test.go

  No findings.

  Summary

  HIGH: 0, MEDIUM: 0, LOW: 0. Highest severity: none.

  The test follows the conventions:
  - Naming: TestLoadTripsReturnsRecentTrips says what it tests.
  - Errors: the error is checked with t.Fatalf before rides is used.
  - Assertions: it checks both the number of rides and the ride ID.
  - Isolation: it uses a local httptest.Server and closes it with t.Cleanup.
  - Formatting: it is laid out as gofmt would format it.

  Target: PR #4 (head baa1692) in
  nebius-academy-templates/go-pr-review-practice, file
  internal/service/trip_service_test.go only.

  The GitHub connection is read-only, so I couldn't post this review even if you
  confirm.

  Post this review? Reply yes or post to confirm.

✻ Baked for 17s · done 2:08 PM

❯ /export                                                                       
  ⎿  Conversation copied to clipboard
```

#### Tool events (from the Claude Code session log)

```text
TOOL CALL: Bash {"command": "cat review-target.json; ls skills; cat fixture-policy.md 2>/dev/null | head -50", "description": "Read review target config and skills list"}

TOOL CALL: ToolSearch {"query": "select:mcp__github__pull_request_read", "max_results": 1}

TOOL RESULT:


TOOL CALL: Skill {"skill": "test-conventions"}

TOOL RESULT:
{
  "mode": "github",
  "owner": "nebius-academy-templates",
  "repo": "go-pr-review-practice",
  "pr_numbers": {
    "1": 1,
    "2": 2,
    "3": 3,
    "4": 4,
    "5": 5
  },
  "tool": "claude",
  "_tool_comment": "Choose before Practice 1: codex (Codex CLI or desktop app) or claude (Claude Code)."
}
app-conventions.md
architecture-guidelines.md
review-pr.md
test-conventions.md
# Fixture policy
These changes are intentionally flawed teaching inputs, never production code.
All credential strings are synthetic and cannot authenticate. Still report their placement
and use as HIGH in this controlled exercise. In real reviews, distinguish fixtures from
actual credentials. Do not reproduce a credential value in a review or trace.
PR-05's external-service test is skipped unless RUN_LIVE_TESTS=1; keep it disabled.
Every PR is independently based on base/, not on the preceding PR.

TOOL RESULT:
Launching skill: test-conventions

TOOL CALL: Bash {"command": "cat review-target.json; ls skills; cat fixture-policy.md 2>/dev/null | head -50", "description": "Read review target config and skills list"}

TOOL CALL: ToolSearch {"query": "select:mcp__github__pull_request_read", "max_results": 1}

TOOL CALL: Skill {"skill": "test-conventions"}

TOOL RESULT:


TOOL RESULT:
{
  "mode": "github",
  "owner": "nebius-academy-templates",
  "repo": "go-pr-review-practice",
  "pr_numbers": {
    "1": 1,
    "2": 2,
    "3": 3,
    "4": 4,
    "5": 5
  },
  "tool": "claude",
  "_tool_comment": "Choose before Practice 1: codex (Codex CLI or desktop app) or claude (Claude Code)."
}
app-conventions.md
architecture-guidelines.md
review-pr.md
test-conventions.md
# Fixture policy
These changes are intentionally flawed teaching inputs, never production code.
All credential strings are synthetic and cannot authenticate. Still report their placement
and use as HIGH in this controlled exercise. In real reviews, distinguish fixtures from
actual credentials. Do not reproduce a credential value in a review or trace.
PR-05's external-service test is skipped unless RUN_LIVE_TESTS=1; keep it disabled.
Every PR is independently based on base/, not on the preceding PR.

TOOL RESULT:
Launching skill: test-conventions

TOOL CALL: Bash {"command": "cat skills/test-conventions.md", "description": "Read test conventions file"}

TOOL CALL: mcp__github__pull_request_read {"method": "get", "owner": "nebius-academy-templates", "repo": "go-pr-review-practice", "pullNumber": 4}

TOOL CALL: mcp__github__pull_request_read {"method": "get_diff", "owner": "nebius-academy-templates", "repo": "go-pr-review-practice", "pullNumber": 4}

TOOL RESULT:
## Rules

Scope: apply these rules to changed files whose basename ends in `_test.go`,
including tests colocated under `internal/`. Do not apply them to other `.go` files.

Naming - `[LOW]`:
- Test functions have the form `TestXxx(t *testing.T)`.
- The name says what behavior is tested: `TestLoadRidesReturnsRepositoryRides`,
  not `Test1`, `TestStuff` or `TestLoadRides2`.
- Cases in a table-driven test have a descriptive `name` and run with `t.Run`.

Assertions - `[MEDIUM]`:
- Check the error before using the result: fail with `t.Fatalf` when `err != nil`
  and the test cannot continue.
- Assert the observable result: the returned value, the error identity with
  `errors.Is` or `errors.As`, or the call recorded by a fake.
- A test that has no assertion, or only prints the result, is a finding.

Isolation - `[MEDIUM]`:
- No real external services: no real network hosts, databases or credentials.
  Use in-package fakes, `httptest.Server` or `t.TempDir`.
- No shared mutable package-level state between tests.
- No dependency on test order: every test sets up what it needs.
- Clean up what the test creates with `t.Cleanup` or `defer`.

Allowed and not required:
- A local `httptest.Server` and in-package fakes are allowed. A local
  `httptest` server is not an external service.
- Do not require doc comments on test functions or unexported test helpers.
- Python rules such as `snake_case` test names or type hints do not apply to Go.

## Template

Compliant:

```go
func TestConvertReturnsRateFromSource(t *testing.T) {
	source := fakeRateSource{rate: 1.25}
	converter := NewConverter(source)

	got, err := converter.Convert(context.Background(), 100)
	if err != nil {
		t.Fatalf("Convert() error = %v", err)
	}
	if want := 125.0; got != want {
		t.Errorf("Convert() = %v, want %v", got, want)
	}
}
```

Non-compliant:

```go
var sharedClient = &http.Client{}

func Test1(t *testing.T) {
	resp, _ := sharedClient.Get("https://api.example.com/rates")
	body, _ := io.ReadAll(resp.Body)
	fmt.Println(string(body))
}
```

What is wrong in the non-compliant test:
- `Test1` does not say what behavior is tested.
- Both errors are discarded, and `resp` is used before any error check.
- The test only prints the body and asserts nothing.
- It calls a real external host and relies on a shared package-level client.
- The response body is never closed or cleaned up.

TOOL RESULT:
{"additions":57,"base":{"ref":"main","repo":{"full_name":"nebius-academy-templates/go-pr-review-practice"},"sha":"cb7a691656d4881e5d045a2308dbaf0d5de52c69"},"body":"## Summary\n\nAdd an application service for retrieving recent trips from the existing API client.\n\n## Changes\n\n- Added `TripService`.\n- Added a constructor for supplying the API client.\n- Added contextual error wrapping when trip loading fails.\n- Added a test using a local HTTP test server.\n\n## Testing\n\n- `go test ./...`\n- `go vet ./...`","changed_files":2,"commits":2,"created_at":"2026-10-20T13:54:08Z","draft":false,"head":{"ref":"pr-04","repo":{"full_name":"nebius-academy-templates/go-pr-review-practice"},"sha":"baa1692dbb1ee9e1c1cb91e2428aebbe38ec1a95"},"html_url":"https://github.com/nebius-academy-templates/go-pr-review-practice/pull/4","mergeable_state":"clean","merged":false,"number":4,"state":"open","title":"Add trip loading service","updated_at":"2026-10-20T13:54:08Z","user":{"avatar_url":"https://avatars.githubusercontent.com/u/172391715?v=4","id":172391715,"login":"ispaniuktripleten","profile_url":"https://github.com/ispaniuktripleten"}}

TOOL RESULT:
{"diff":"diff --git a/internal/service/trip_service.go b/internal/service/trip_service.go\nnew file mode 100644\nindex 0000000..e42b38d\n--- /dev/null\n+++ b/internal/service/trip_service.go\n@@ -0,0 +1,24 @@\n+package service\n+\n+import (\n+\t\"context\"\n+\t\"fmt\"\n+\n+\t\"example.com/go-pr-review-practice/internal/api\"\n+\t\"example.com/go-pr-review-practice/internal/model\"\n+)\n+\n+// TripService loads a user's trips.\n+type TripService struct{ client *api.Client }\n+\n+// NewTripService constructs a service with its data client.\n+func NewTripService(client *api.Client) *TripService { return &TripService{client: client} }\n+\n+// LoadTrips retrieves the recent trips.\n+func (s *TripService) LoadTrips(ctx context.Context) ([]model.Ride, error) {\n+\trides, err := s.client.RecentRides(ctx)\n+\tif err != nil {\n+\t\treturn nil, fmt.Errorf(\"load trips: %w\", err)\n+\t}\n+\treturn rides, nil\n+}\ndiff --git a/internal/service/trip_service_test.go b/internal/service/trip_service_test.go\nnew file mode 100644\nindex 0000000..0192703\n--- /dev/null\n+++ b/internal/service/trip_service_test.go\n@@ -0,0 +1,33 @@\n+package service\n+\n+import (\n+\t\"context\"\n+\t\"fmt\"\n+\t\"net/http\"\n+\t\"net/http/httptest\"\n+\t\"testing\"\n+\n+\t\"example.com/go-pr-review-practice/internal/api\"\n+)\n+\n+func TestLoadTripsReturnsRecentTrips(t *testing.T) {\n+\tserver := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {\n+\t\tif r.URL.Path != \"/rides\" {\n+\t\t\thttp.NotFound(w, r)\n+\t\t\treturn\n+\t\t}\n+\t\tw.Header().Set(\"Content-Type\", \"application/json\")\n+\t\tif _, err := fmt.Fprint(w, `[{\"id\":\"ride-1\"}]`); err != nil {\n+\t\t\tt.Errorf(\"write response: %v\", err)\n+\t\t}\n+\t}))\n+\tt.Cleanup(server.Close)\n+\tsvc := NewTripService(api.NewClient(server.Client(), server.URL))\n+\trides, err := svc.LoadTrips(context.Background())\n+\tif err != nil {\n+\t\tt.Fatalf(\"LoadTrips() error = %v\", err)\n+\t}\n+\tif len(rides) != 1 || rides[0].ID != \"ride-1\" {\n+\t\tt.Fatalf(\"LoadTrips() = %v, want ride-1\", rides)\n+\t}\n+}\n"}
```

### Test 3 - workflow skill /review-pr 1

#### Conversation (Claude Code /export)

```text
▗ ▗   ▖ ▖  Claude Code v2.1.331
           Opus 5.5 · Claude Team
 ▘▘   ▝▝   ~/inap-tests/building-your-go-agent/building-your-go-agent


❯ /review-pr 1                                                                  

  Listed 1 directory (ctrl+o to expand)

⏺ Main-agent review, no delegation. Fetching PR 1.

  Called github 2 times (ctrl+o to expand)

⏺ Skill(app-conventions)
  ⎿  Loaded skill

⏺ Skill(test-conventions)
  ⎿  Loaded skill

  Read 1 file (ctrl+o to expand)

⏺ I loaded app-conventions for internal/service/ride_history_service.go and
  test-conventions for internal/service/ride_history_service_test.go. The review
  was done in the main agent with no subagents, as this practice requires. I
  made two passes over the diff.

  internal/service/ride_history_service.go

  No findings.

  internal/service/ride_history_service_test.go

  No findings.

  Summary

  HIGH: 0, MEDIUM: 0, LOW: 0. Highest severity: none.

  ---

  Target: PR #1 "Add ride history service" in
  nebius-academy-templates/go-pr-review-practice, head SHA
  2d551765be07e61e70f70245c9f7be2943f901f4.

  The GitHub connection is read-only and has no tool for writing reviews. Even
  if you confirm, I can't post this, and I won't try to get around that.

  Post this review? Reply yes or post to confirm.

✻ Worked for 20s · done 2:10 PM

❯ /export                                                                       
  ⎿  Conversation copied to clipboard

❯ /export /tmp/session-3c.txt                                                   
  ⎿  Conversation exported to /tmp/session-3c.txt

❯ no                                                                            

⏺ I posted nothing to PR #1 in nebius-academy-templates/go-pr-review-practice.

✻ Crunched for 2s · done 2:12 PM

❯ /export /tmp/session-3c.txt                                                   
  ⎿  Couldn't export conversation: the file already exists · run /export to
     overwrite it

❯ /export                                                                       
  ⎿  Conversation exported to /Users/anastasiakornelik/inap-tests/building-your-
     go-agent/building-your-go-agent/session-3c.txt
```

#### Tool events (from the Claude Code session log)

```text
TOOL CALL: Bash {"command": "cat skills/review-pr.md && echo ---- && cat review-target.json && echo ---- && cat fixture-policy.md 2>/dev/null; ls", "description": "Read review workflow, target config and fixture policy"}

TOOL RESULT:
## Steps

1. Resolve the target. Take the PR number from the argument. If it is missing or
   is not one of the numbers in `review-target.json`, stop and ask for it. Read
   `review-target.json` for the mode, owner, repository and PR number mapping.

2. Fetch the change.
   - In `github` mode, call the GitHub MCP tool `pull_request_read` with `get` for
     the title and head SHA, then with `get_diff` for the diff. Call `get_files`
     when you need the list of changed files.
   - In `local` mode, read the matching file under `samples/`. There is no head
     SHA in local mode.
   - Never substitute a local sample for a live PR. If the GitHub MCP server is
     not connected, stop and say so.

3. Route the changed files by type and load only the conventions that are needed.
   - A `.go` file whose basename does not end in `_test.go`: load `app-conventions`.
   - A file whose basename ends in `_test.go`: load `test-conventions`.
   - Any other file: load no convention skill.
   - If the request names a file scope, review only the files in that scope.
   - Say which convention skills you loaded and for which files.

4. Review each file in scope. Apply the review criteria in `AGENTS.md`, including its security rules, the
   conventions loaded for that file, and `fixture-policy.md`. Report only issues
   you can point to in the diff, with the file and the line or hunk. Do not apply
   application conventions to test files or test conventions to application files.

5. Check the findings once more against the diff. Drop any finding you cannot
   point to, and merge duplicates. Make at most two review passes in total.

6. Consolidate one review in the output format from `AGENTS.md`: a section for
   each file in scope, `No findings.` for a clean file, and a closing `## Summary`
   with the counts by severity and the highest severity.

7. Present the review. Name the target: the PR number, the repository and the
   head SHA, or the local file. End with
   "Post this review? Reply yes or post to confirm." Do not post.

8. Follow the publish gate in `AGENTS.md`. Post only after an exact `yes` or
   `post`, and only with a write tool that the connection provides. Never approve,
   merge or close the PR.

Do the whole review in the main agent. Do not delegate to subagents in this
practice.
----
{
  "mode": "github",
  "owner": "nebius-academy-templates",
  "repo": "go-pr-review-practice",
  "pr_numbers": {
    "1": 1,
    "2": 2,
    "3": 3,
    "4": 4,
    "5": 5
  },
  "tool": "claude",
  "_tool_comment": "Choose before Practice 1: codex (Codex CLI or desktop app) or claude (Claude Code)."
}
----
# Fixture policy
These changes are intentionally flawed teaching inputs, never production code.
All credential strings are synthetic and cannot authenticate. Still report their placement
and use as HIGH in this controlled exercise. In real reviews, distinguish fixtures from
actual credentials. Do not reproduce a credential value in a review or trace.
PR-05's external-service test is skipped unless RUN_LIVE_TESTS=1; keep it disabled.
Every PR is independently based on base/, not on the preceding PR.
AGENTS.md
CLAUDE.md
README.md
docs
fixture-policy.md
practice-description.md
results
review-target.json
review_history.md
reviewers
samples
setup
skills

TOOL CALL: Bash {"command": "cd /Users/anastasiakornelik/inap-tests/building-your-go-agent/building-your-go-agent && cat skills/review-pr.md && echo ---- && cat review-target.json && echo ---- && cat fixture-policy.md 2>/dev/null; ls", "description": "Read review workflow, target config and fixture policy"}

TOOL CALL: ToolSearch {"query": "select:mcp__github__pull_request_read", "max_results": 1}

TOOL RESULT:


TOOL RESULT:


TOOL CALL: mcp__github__pull_request_read {"method": "get", "owner": "nebius-academy-templates", "repo": "go-pr-review-practice", "pullNumber": 1}

TOOL CALL: mcp__github__pull_request_read {"method": "get_diff", "owner": "nebius-academy-templates", "repo": "go-pr-review-practice", "pullNumber": 1}

TOOL RESULT:
{"additions":68,"base":{"ref":"main","repo":{"full_name":"nebius-academy-templates/go-pr-review-practice"},"sha":"cb7a691656d4881e5d045a2308dbaf0d5de52c69"},"body":"## Summary\n\nAdd an application service for loading recent rides through a repository abstraction.\n\n## Changes\n\n- Added the `RideRepository` interface.\n- Added `RideHistoryService` with constructor-based dependency injection.\n- Added error wrapping for repository failures.\n- Added unit tests for successful and failed repository calls.\n\n## Testing\n\n- `go test ./...`\n- `go vet ./...`","changed_files":2,"commits":2,"created_at":"2026-10-20T13:46:33Z","draft":false,"head":{"ref":"pr-01","repo":{"full_name":"nebius-academy-templates/go-pr-review-practice"},"sha":"2d551765be07e61e70f70245c9f7be2943f901f4"},"html_url":"https://github.com/nebius-academy-templates/go-pr-review-practice/pull/1","mergeable_state":"clean","merged":false,"number":1,"state":"open","title":"Add ride history service","updated_at":"2026-10-20T13:46:33Z","user":{"avatar_url":"https://avatars.githubusercontent.com/u/172391715?v=4","id":172391715,"login":"ispaniuktripleten","profile_url":"https://github.com/ispaniuktripleten"}}

TOOL RESULT:
{"diff":"diff --git a/internal/service/ride_history_service.go b/internal/service/ride_history_service.go\nnew file mode 100644\nindex 0000000..e191eb0\n--- /dev/null\n+++ b/internal/service/ride_history_service.go\n@@ -0,0 +1,30 @@\n+package service\n+\n+import (\n+\t\"context\"\n+\t\"fmt\"\n+\n+\t\"example.com/go-pr-review-practice/internal/model\"\n+)\n+\n+// RideRepository provides the data needed by RideHistoryService.\n+type RideRepository interface {\n+\tRecentRides(context.Context) ([]model.Ride, error)\n+}\n+\n+// RideHistoryService loads recent trips through a repository.\n+type RideHistoryService struct{ repository RideRepository }\n+\n+// NewRideHistoryService constructs a service with its repository.\n+func NewRideHistoryService(repository RideRepository) *RideHistoryService {\n+\treturn &RideHistoryService{repository: repository}\n+}\n+\n+// LoadRides returns recent rides or the repository error with context.\n+func (s *RideHistoryService) LoadRides(ctx context.Context) ([]model.Ride, error) {\n+\trides, err := s.repository.RecentRides(ctx)\n+\tif err != nil {\n+\t\treturn nil, fmt.Errorf(\"load rides: %w\", err)\n+\t}\n+\treturn rides, nil\n+}\ndiff --git a/internal/service/ride_history_service_test.go b/internal/service/ride_history_service_test.go\nnew file mode 100644\nindex 0000000..c955995\n--- /dev/null\n+++ b/internal/service/ride_history_service_test.go\n@@ -0,0 +1,38 @@\n+package service\n+\n+import (\n+\t\"context\"\n+\t\"errors\"\n+\t\"reflect\"\n+\t\"testing\"\n+\n+\t\"example.com/go-pr-review-practice/internal/model\"\n+)\n+\n+type fakeRideRepository struct {\n+\trides []model.Ride\n+\terr   error\n+}\n+\n+func (f fakeRideRepository) RecentRides(context.Context) ([]model.Ride, error) { return f.rides, f.err }\n+\n+func TestLoadRidesReturnsRepositoryRides(t *testing.T) {\n+\twant := []model.Ride{{ID: \"ride-1\"}, {ID: \"ride-2\"}}\n+\tsvc := NewRideHistoryService(fakeRideRepository{rides: want})\n+\tgot, err := svc.LoadRides(context.Background())\n+\tif err != nil {\n+\t\tt.Fatalf(\"LoadRides() error = %v\", err)\n+\t}\n+\tif !reflect.DeepEqual(got, want) {\n+\t\tt.Fatalf(\"LoadRides() = %v, want %v\", got, want)\n+\t}\n+}\n+\n+func TestLoadRidesPreservesRepositoryError(t *testing.T) {\n+\twant := errors.New(\"repository unavailable\")\n+\tsvc := NewRideHistoryService(fakeRideRepository{err: want})\n+\t_, err := svc.LoadRides(context.Background())\n+\tif !errors.Is(err, want) {\n+\t\tt.Fatalf(\"LoadRides() error = %v, want wrapped %v\", err, want)\n+\t}\n+}\n"}

TOOL CALL: Skill {"skill": "app-conventions"}

TOOL CALL: Skill {"skill": "test-conventions"}

TOOL RESULT:
Launching skill: app-conventions

TOOL RESULT:
Launching skill: test-conventions

TOOL CALL: Skill {"skill": "app-conventions"}

TOOL RESULT:
Launching skill: app-conventions

TOOL CALL: Skill {"skill": "test-conventions"}

TOOL RESULT:
Launching skill: test-conventions

TOOL CALL: Bash {"command": "cat skills/app-conventions.md && echo ---- && cat skills/test-conventions.md", "description": "Read app and test convention files"}

TOOL RESULT:
## Rules

Scope: apply these rules to changed `.go` files whose basename does not end in
`_test.go`. Do not apply them to test files; those follow `test-conventions`.

Naming - `[LOW]`:
- Exported identifiers use `MixedCaps`; unexported identifiers use `mixedCaps`.
  No underscores and no `ALL_CAPS` constants.
- Initialisms keep one case: `ID`, `URL`, `HTTP`, `API`, `JSON`, `SQL`
  (`userID`, `ParseURL`, not `userId`, `ParseUrl`).
- Package names are short, lowercase, and have no underscores.

Doc comments - `[LOW]`:
- Every exported type, function, method, constant and variable has a doc comment.
- The comment is a full sentence that starts with the name it documents.
- Unexported identifiers do not need a doc comment.

Formatting - `[LOW]`:
- Code matches `gofmt` output: tabs for indentation, gofmt spacing and alignment.

Errors - `[MEDIUM]` for a discarded or unchecked error, `[LOW]` for wrapping and error string style:
- Never discard an error: no `_ = f()` and no `value, _ := f()`.
- Check the error right after the call and return early.
- When adding context to an error, wrap it with `fmt.Errorf("...: %w", err)` so callers can use
  `errors.Is` and `errors.As`. Do not use `%v` or `%s` for the wrapped error.
- Error strings start with a lowercase letter and have no trailing punctuation.
- The error is the last return value.

Context - `[MEDIUM]`:
- A function that does I/O or calls one that does takes `context.Context` as its
  first parameter; when the parameter is named, the name is `ctx`.
- Pass the received context on to the calls that accept one.
- Do not call `context.Background()` or `context.TODO()` where a context is
  already available, and do not store a context in a struct field.

Not applicable:
- Python rules such as `snake_case`, type hints, bare `except` or mutable default
  arguments do not apply to Go. Never report them.

Severity:
- `[MEDIUM]` - a discarded or unchecked error, a dropped or replaced context.
- `[LOW]` - naming, initialisms, doc comments, formatting, error string style.

## Template

Compliant:

```go
// UserStore loads users from the database.
type UserStore struct {
	db *sql.DB
}

// FindByID returns the user with the given ID.
func (s *UserStore) FindByID(ctx context.Context, userID string) (*User, error) {
	user, err := s.queryUser(ctx, userID)
	if err != nil {
		return nil, fmt.Errorf("find user %s: %w", userID, err)
	}
	return user, nil
}
```

Non-compliant:

```go
type User_Store struct {
	db *sql.DB
}

func (s *User_Store) FindById(userId string) *User {
	user, _ := s.queryUser(context.Background(), userId)
	return user
}
```

What is wrong in the non-compliant snippet:
- `User_Store` uses an underscore, and `FindById` and `userId` break the `ID`
  initialism.
- The exported type and method have no doc comments.
- The error from `queryUser` is discarded and never returned.
- The method takes no `ctx` and calls `context.Background()` instead.
----
## Rules

Scope: apply these rules to changed files whose basename ends in `_test.go`,
including tests colocated under `internal/`. Do not apply them to other `.go` files.

Naming - `[LOW]`:
- Test functions have the form `TestXxx(t *testing.T)`.
- The name says what behavior is tested: `TestLoadRidesReturnsRepositoryRides`,
  not `Test1`, `TestStuff` or `TestLoadRides2`.
- Cases in a table-driven test have a descriptive `name` and run with `t.Run`.

Assertions - `[MEDIUM]`:
- Check the error before using the result: fail with `t.Fatalf` when `err != nil`
  and the test cannot continue.
- Assert the observable result: the returned value, the error identity with
  `errors.Is` or `errors.As`, or the call recorded by a fake.
- A test that has no assertion, or only prints the result, is a finding.

Isolation - `[MEDIUM]`:
- No real external services: no real network hosts, databases or credentials.
  Use in-package fakes, `httptest.Server` or `t.TempDir`.
- No shared mutable package-level state between tests.
- No dependency on test order: ev
[output truncated]

TOOL CALL: Bash {"command": "cd /Users/anastasiakornelik/inap-tests/building-your-go-agent/building-your-go-agent && cat skills/app-conventions.md && echo ---- && cat skills/test-conventions.md", "description": "Read app and test convention files"}
```

