# jira-buddy

A friendly helper for Jira. Tell it about a problem in plain words, and it:

- finds duplicates, the right epic, related Confluence pages, Product
  Discovery ideas and known issues in Service Management,
- drafts a clear ticket with a plain half for everyone and a technical half
  for the person doing the work,
- splits big requests into an epic with smaller tickets,
- updates status, labels and assignee safely,
- posts short, plain progress comments as the work moves.

Nothing is created, changed or commented in Jira until you say yes.

## Needs

The Atlassian connector (MCP) for Claude, signed in to your Atlassian site.
jira-buddy ships no connector of its own and holds no credentials; sign-in is
handled by the connector.

## Use

Say things like "file a ticket for the broken export", "split this into
tickets", "move this ticket to done" or "comment that it's live".

On first use it looks up your Atlassian site and projects, asks you to pick
where tickets go, and saves a small settings file at
`.claude/jira-buddy.json` in your project, so your team can share it.

## What it runs, reads and sends

- **Atlassian:** it reads and writes Jira issues, comments and links, and
  searches Confluence, only through the Atlassian connector you connected,
  and only after you approve each change.
- **Your project:** when a code project is open, it reads files to find the
  cause of a problem. It never changes code.
- **Files it writes:** only `.claude/jira-buddy.json`, after you approve it.
  The file holds site and project names, no secrets.
- **Nothing else:** no other network calls, no scripts, no hooks, no
  telemetry.

## License

MIT. Made by Kendoo R&D Consulting. Jira and Confluence are trademarks of
Atlassian; this plugin is not made or endorsed by Atlassian.
