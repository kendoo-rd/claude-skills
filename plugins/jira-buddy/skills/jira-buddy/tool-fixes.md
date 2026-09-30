# Known Atlassian connector problems and fixes

These are real problems seen with the Atlassian connector (MCP). Check this
list before the first create, edit, search, label or status call.

## Creating a ticket

- **Line breaks get lost on create.** A description sent with
  `createJiraIssue` often comes back with a literal `\n` instead of real line
  breaks, so the ticket shows as one block of text. Fix: right after
  `createJiraIssue`, call `editJiraIssue` with the same description inside
  `fields: {description: "..."}`. The edit keeps the line breaks. Think of
  create as "make the ticket and title", and edit as "write the body". Skip
  this only for one-line descriptions.
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
- **Comments cannot be edited or deleted** through the connector. Get it right
  before posting. If a comment is wrong, ask the user to fix it in Jira.
- **Files cannot be attached** through the connector. Keep content in the
  description, or give the user the text to drag into Jira.

## Links in pull requests and commits

- **Bare ticket keys link automatically.** Any `KEY-123` in a pull request
  description or commit message links that pull request to the ticket, even a
  ticket it does not belong to. A pull request link can be removed by editing
  the description; a commit message link needs the commit to be rewritten
  and force-pushed. Mention other tickets in words unless you want the link.
  Use the bare key only for the pull request's own ticket.
