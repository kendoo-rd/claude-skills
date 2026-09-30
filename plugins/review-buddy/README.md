# review-buddy

A careful second pair of eyes on a code change. It reviews a pull request, a
branch or uncommitted work in five passes:

1. Does it do what was asked (ticket, pull request text or plan)?
2. Is it correct: logic, error handling, race conditions, memory and resource
   leaks?
3. Is it well designed, secure and fast, with no hidden breaking changes or
   risky new libraries?
4. Does it follow the codebase's own conventions and patterns?
5. Do the tests really test the change?

Every finding points to a file and line. The report ends with a verdict
(approve, approve with changes, request changes, or cannot conclude) and a
short summary anyone can read.

## Use

Ask "review my changes", "review this branch" or "review PR #12".

## What it runs, reads and sends

- **Reads:** the changed files and nearby code in your repository.
- **Runs:** `git` commands that only read (diff, log, status, show, fetch of
  the pull request's commit). With the GitHub CLI (`gh`) installed, it reads
  pull request details. It may run your project's own test command once, and
  only when the checkout is the version under review.
- **Never:** edits code, commits, pushes or switches your branch.
- **Sends:** nothing by itself. It posts a review comment to the pull request
  only when you ask, after showing you the exact text.
- **No** scripts, hooks, MCP servers or telemetry.

## License

MIT. Made by Kendoo R&D Consulting.
