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
