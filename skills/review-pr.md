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

3. Set the review scope. List the changed files. If the request names a file
   scope, keep only those files. Keep the complete diff for the files in scope.

4. Delegate the style review to the `style-reviewer` subagent. Pass it the
   target, the head SHA, the repository root, the review scope, the list of
   changed files and the complete in-scope diff. Wait for its result.

5. Delegate the security review to the `security-reviewer` subagent with the
   same input. Wait for its result.

6. Delegate the architecture review to the `architecture-reviewer` subagent with
   the same input. Wait for its result.

7. Merge the three results into one review in the output format from `AGENTS.md`.
   - Keep a section for each file in scope, and write `No findings.` for a file
     that no reviewer flagged.
   - Keep the severity each reviewer gave, and say which reviewer reported the
     finding: style, security or architecture.
   - If two reviewers report the same line and issue, keep one finding with the
     higher severity. A hardcoded credential is reported once, as a security
     finding.
   - Drop any finding you cannot point to in the diff.
   - Never reproduce a credential value. Follow `fixture-policy.md`.
   - End with `## Summary`: the counts by severity and the highest severity.

8. Present the review. Name the target: the PR number, the repository and the
   head SHA, or the local file. End with
   "Post this review? Reply yes or post to confirm." Do not post.

9. Follow the publish gate in `AGENTS.md`. Post only after an exact `yes` or
   `post`, and only with a write tool that the connection provides. Never approve,
   merge or close the PR.

Delegation rules:
- Run the subagents one at a time, in the order style, security, architecture.
  Wait for each result before starting the next.
- Use the real named subagents. Do not do their review yourself. If a named
  subagent is unavailable, report the missing subagent and stop instead of
  simulating its review.
- One delegation round is one review pass. Make at most two passes in total.
- Subagents never ask the user anything and never post. The confirmation gate
  stays in the main agent.
- Pass every subagent the same complete in-scope diff and the same changed-file
  list, including test files.
- If any subagent fails or returns no usable result, stop. Report an incomplete
  review, name the subagent that failed, and do not merge or offer to post.
- A hardcoded credential and the matching Configuration guideline become one
  `[HIGH]` finding in the merged review, with the value masked as `<redacted>`.
- In github mode, always make three `pull_request_read` calls before delegating:
  `get`, `get_diff` and `get_files`. Take the changed-file list from `get_files`.
