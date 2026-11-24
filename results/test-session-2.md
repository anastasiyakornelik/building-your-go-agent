# Practice 2 — actual session evidence

Tool/model/version and date: Claude Code v2.1.331, Opus 5.5, 2026-11-24
Target, mode and head SHA when live: nebius-academy-templates/go-pr-review-practice PR #1, github mode, head SHA 2d551765be07e61e70f70245c9f7be2943f901f4
MCP endpoint: https://api.githubcopilot.com/mcp/ with X-MCP-Readonly: true, 28 read-only tools advertised; tool used: mcp__github__pull_request_read (get, get_diff)

## Recorded conversation and tool events

### Conversation (Claude Code /export)

```text
▗ ▗   ▖ ▖  Claude Code v2.1.331
           Opus 5.5 · Claude Team
 ▘▘   ▝▝   ~/inap-tests/building-your-go-agent/building-your-go-agent


❯ Review PR #1 from                                                             
  https://github.com/nebius-academy-templates/go-pr-review-practice according   
  to AGENTS.md.                                                                 

  Read 1 file, called github 2 times, ran 1 shell command (ctrl+o to expand)

⏺ I reviewed both files in PR #1 against AGENTS.md in two passes and found
  nothing to flag. I also ran gofmt on the lines whose formatting could be in
  doubt (the one-line struct, the aligned struct fields and the one-line
  method), and it reported no changes.

  internal/service/ride_history_service.go

  No findings.

  internal/service/ride_history_service_test.go

  No findings.

  Summary

  HIGH: 0, MEDIUM: 0, LOW: 0. Highest severity: none.

  The test functions TestLoadRidesReturnsRepositoryRides and
  TestLoadRidesPreservesRepositoryError have no doc comments. I didn't flag them
  because Go doesn't expect doc comments on TestXxx functions in _test.go
  files.

  Target: PR #1 (Add ride history service) in
  nebius-academy-templates/go-pr-review-practice, head SHA 2d55176.

  Post this review? Reply yes or post to confirm.

✻ Brewed for 18s · done 1:29 PM

❯ no                                                                            

⏺ Okay, I didn't post anything. The review of PR #1 in
  nebius-academy-templates/go-pr-review-practice isn't on GitHub.

✻ Cogitated for 1s · done 1:32 PM

❯ /export                                                                       
  ⎿  Conversation copied to clipboard

※ recap: You asked me to review PR #1 in go-pr-review-practice against AGENTS.md; I found nothing to flag and posted nothing, since you replied no. Next, you could ask me to review PR #2. (turn off recaps in /config)

❯ /export                                                                       
  ⎿  Conversation copied to clipboard
```

### Tool events (from the Claude Code session log)

```text
TOOL CALL: Bash {"command": "cat review-target.json && echo ---- && cat fixture-policy.md", "description": "Read review target and fixture policy"}

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
----
# Fixture policy
These changes are intentionally flawed teaching inputs, never production code.
All credential strings are synthetic and cannot authenticate. Still report their placement
and use as HIGH in this controlled exercise. In real reviews, distinguish fixtures from
actual credentials. Do not reproduce a credential value in a review or trace.
PR-05's external-service test is skipped unless RUN_LIVE_TESTS=1; keep it disabled.
Every PR is independently based on base/, not on the preceding PR.

TOOL CALL: Bash {"command": "cd /Users/anastasiakornelik/inap-tests/building-your-go-agent/building-your-go-agent && cat review-target.json && echo ---- && cat fixture-policy.md", "description": "Read review target and fixture policy"}

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
----
# Fixture policy
These changes are intentionally flawed teaching inputs, never production code.
All credential strings are synthetic and cannot authenticate. Still report their placement
and use as HIGH in this controlled exercise. In real reviews, distinguish fixtures from
actual credentials. Do not reproduce a credential value in a review or trace.
PR-05's external-service test is skipped unless RUN_LIVE_TESTS=1; keep it disabled.
Every PR is independently based on base/, not on the preceding PR.

TOOL CALL: mcp__github__pull_request_read {"method": "get", "owner": "nebius-academy-templates", "repo": "go-pr-review-practice", "pullNumber": 1}

TOOL CALL: mcp__github__pull_request_read {"method": "get_diff", "owner": "nebius-academy-templates", "repo": "go-pr-review-practice", "pullNumber": 1}

TOOL RESULT:
{"additions":68,"base":{"ref":"main","repo":{"full_name":"nebius-academy-templates/go-pr-review-practice"},"sha":"cb7a691656d4881e5d045a2308dbaf0d5de52c69"},"body":"## Summary\n\nAdd an application service for loading recent rides through a repository abstraction.\n\n## Changes\n\n- Added the `RideRepository` interface.\n- Added `RideHistoryService` with constructor-based dependency injection.\n- Added error wrapping for repository failures.\n- Added unit tests for successful and failed repository calls.\n\n## Testing\n\n- `go test ./...`\n- `go vet ./...`","changed_files":2,"commits":2,"created_at":"2026-10-20T13:46:33Z","draft":false,"head":{"ref":"pr-01","repo":{"full_name":"nebius-academy-templates/go-pr-review-practice"},"sha":"2d551765be07e61e70f70245c9f7be2943f901f4"},"html_url":"https://github.com/nebius-academy-templates/go-pr-review-practice/pull/1","mergeable_state":"clean","merged":false,"number":1,"state":"open","title":"Add ride history service","updated_at":"2026-10-20T13:46:33Z","user":{"avatar_url":"https://avatars.githubusercontent.com/u/172391715?v=4","id":172391715,"login":"ispaniuktripleten","profile_url":"https://github.com/ispaniuktripleten"}}

TOOL RESULT:
{"diff":"diff --git a/internal/service/ride_history_service.go b/internal/service/ride_history_service.go\nnew file mode 100644\nindex 0000000..e191eb0\n--- /dev/null\n+++ b/internal/service/ride_history_service.go\n@@ -0,0 +1,30 @@\n+package service\n+\n+import (\n+\t\"context\"\n+\t\"fmt\"\n+\n+\t\"example.com/go-pr-review-practice/internal/model\"\n+)\n+\n+// RideRepository provides the data needed by RideHistoryService.\n+type RideRepository interface {\n+\tRecentRides(context.Context) ([]model.Ride, error)\n+}\n+\n+// RideHistoryService loads recent trips through a repository.\n+type RideHistoryService struct{ repository RideRepository }\n+\n+// NewRideHistoryService constructs a service with its repository.\n+func NewRideHistoryService(repository RideRepository) *RideHistoryService {\n+\treturn &RideHistoryService{repository: repository}\n+}\n+\n+// LoadRides returns recent rides or the repository error with context.\n+func (s *RideHistoryService) LoadRides(ctx context.Context) ([]model.Ride, error) {\n+\trides, err := s.repository.RecentRides(ctx)\n+\tif err != nil {\n+\t\treturn nil, fmt.Errorf(\"load rides: %w\", err)\n+\t}\n+\treturn rides, nil\n+}\ndiff --git a/internal/service/ride_history_service_test.go b/internal/service/ride_history_service_test.go\nnew file mode 100644\nindex 0000000..c955995\n--- /dev/null\n+++ b/internal/service/ride_history_service_test.go\n@@ -0,0 +1,38 @@\n+package service\n+\n+import (\n+\t\"context\"\n+\t\"errors\"\n+\t\"reflect\"\n+\t\"testing\"\n+\n+\t\"example.com/go-pr-review-practice/internal/model\"\n+)\n+\n+type fakeRideRepository struct {\n+\trides []model.Ride\n+\terr   error\n+}\n+\n+func (f fakeRideRepository) RecentRides(context.Context) ([]model.Ride, error) { return f.rides, f.err }\n+\n+func TestLoadRidesReturnsRepositoryRides(t *testing.T) {\n+\twant := []model.Ride{{ID: \"ride-1\"}, {ID: \"ride-2\"}}\n+\tsvc := NewRideHistoryService(fakeRideRepository{rides: want})\n+\tgot, err := svc.LoadRides(context.Background())\n+\tif err != nil {\n+\t\tt.Fatalf(\"LoadRides() error = %v\", err)\n+\t}\n+\tif !reflect.DeepEqual(got, want) {\n+\t\tt.Fatalf(\"LoadRides() = %v, want %v\", got, want)\n+\t}\n+}\n+\n+func TestLoadRidesPreservesRepositoryError(t *testing.T) {\n+\twant := errors.New(\"repository unavailable\")\n+\tsvc := NewRideHistoryService(fakeRideRepository{err: want})\n+\t_, err := svc.LoadRides(context.Background())\n+\tif !errors.Is(err, want) {\n+\t\tt.Fatalf(\"LoadRides() error = %v, want wrapped %v\", err, want)\n+\t}\n+}\n"}

TOOL CALL: Bash {"command": "cd /private/tmp/claude-501/-Users-anastasiakornelik-inap-tests-building-your-go-agent-building-your-go-agent/f5ea82b3-20de-471c-b64c-0e3783b472d4/scratchpad && cat > a.go <<'EOF'\npackage service\n\ntype RideHistoryService struct{ repository RideRepository }\n\ntype fakeRideRepository struct {\n\trides []model.Ride\n\terr   error\n}\n\nfunc (f fakeRideRepository) RecentRides(context.Context) ([]model.Ride, error) { return f.rides, f.err }\nEOF\ngofmt -l a.go; echo \"exit=$?\"", "description": "Check gofmt on formatting-sensitive lines"}

TOOL RESULT:
exit=0
Shell cwd was reset to /Users/anastasiakornelik/inap-tests/building-your-go-agent/building-your-go-agent
```
