# Known Atlassian connector problems and fixes

These are real problems seen with the Atlassian remote connector (MCP),
last tested in September 2026. The connector changes often: a fix below may
no longer be needed, and new tools may exist. Always prefer the tool's own
description. When a problem below does not happen, skip its workaround.

## Creating a ticket

- **Line breaks can get lost on create** (seen up to mid-2026). A
  description sent with `createJiraIssue` sometimes comes back with a literal
  `\n` instead of real line breaks, so the ticket shows as one block of text.
  Check: read the ticket back after creating it. Fix, only if it happened:
  call `editJiraIssue` with the same description inside
  `fields: {description: "..."}`; the edit keeps the line breaks.
- **`description` sits in a different place on create and edit.** On
  `createJiraIssue` it is a top-level parameter; inside `fields` it is
  silently ignored and the description stays empty. On `editJiraIssue` it
  goes inside `fields`. Mixing them up leaves an empty description.
- **Use `contentFormat: "markdown"`** for descriptions, unless you need an
  @-mention that only works as `"adf"` (see `comments.md`).
- **Parent epic:** pass the epic key as `parent`. Priority goes in
  `additional_fields`: `{"priority": {"name": "Medium"}}`.

## Searching

- **Search results are too big.** `searchJiraIssuesUsingJql` returns full
  descriptions even when you ask for a few fields, so most searches with
  several results go over the tool result limit and are saved to a file.
  Asking for fewer fields does not help. Read the saved file with `jq`:

  ```
  jq -r '.issues.nodes[] | [.key, .fields.status.name, .fields.summary] | @tsv' <saved-file>
  ```

  If the shape differs, look at the top-level keys first with `jq 'keys'`.

## Changing a ticket

- **Status ids differ per project.** Two projects can use the same number for
  different statuses. Moving a ticket with an id from memory can silently set
  the wrong status. Call `getTransitionsForJiraIssue` for the ticket every
  time, and pick by name.
- **Labels: no spaces, and edit replaces all of them.** Jira rejects labels
  with spaces; use `kebab-case` (`needs-review`). `editJiraIssue` has no
  "add label": setting `fields: {labels: [...]}` replaces the full list. Read
  the current labels first, merge, and write back the whole list.
- **Editing a comment:** in the tested version, `addCommentToJiraIssue` adds
  a new comment, or updates an existing one when you pass its `commentId`.
  Keep the id returned when you post, so you can fix the same comment later.
  Never fix a comment by posting a second one.
- **Deleting comments and attaching files** were not available in the tested
  version. Check your tool list. If they are missing, ask the user to do it in
  Jira, or keep the content in the description.

## Links in pull requests and commits

- **Bare ticket keys link automatically.** Any `KEY-123` in a pull request
  description or commit message links that pull request to the ticket, even a
  ticket it does not belong to. A pull request link can be removed by editing
  the description; a commit message link needs the commit to be rewritten
  and force-pushed. Mention other tickets in words unless you want the link.
  Use the bare key only for the pull request's own ticket.
