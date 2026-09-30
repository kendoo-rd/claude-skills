# Kendoo Claude Skills

Free skills for Claude Code, made by Kendoo R&D Consulting.

## Install

Add this collection once:

```
/plugin marketplace add kendoo-rd/claude-skills
```

Then install any skill below.

## Skills

### under25

Keeps replies short and simple.

- Every reply is 25 to 30 words.
- Plain B1-level English.
- No technical terms unless they are truly needed.

Claude still does the full work. Only the reply you read is short.

```
/plugin install under25@kendoo-rd
```

Ask for short or plain answers ("keep it short", "B1 English", "no jargon"), or type `/under25`. To use it in every session, add this line to your `CLAUDE.md`:

```
Always follow the under25 skill: every reply 25-30 words, B1-level English.
```

### jira-buddy

A friendly helper for Jira. Tell it about a problem in plain words, and it:

- finds duplicates, the right epic, related Confluence pages, Product
  Discovery ideas and known issues in Service Management,
- drafts a clear ticket with a plain half for everyone and a technical half
  for the person doing the work,
- splits big requests into an epic with smaller tickets,
- updates status, labels and assignee safely,
- posts short, plain progress comments as the work moves.

Nothing is created or changed in Jira until you say yes.

Needs the Atlassian connector for Claude, signed in to your site. On first use
it looks up your projects and saves a small settings file at
`.claude/jira-buddy.json` in your project, so your team can share it.

```
/plugin install jira-buddy@kendoo-rd
```

## License

MIT
