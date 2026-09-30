# The ticket

Two clearly separated halves. The top half is for everyone. The bottom half
is for the person doing the work.

For work with no code (marketing, operations, support), keep the top half and
replace the tech half with "Details": the steps, the people involved, and the
files or pages needed.

## Template

```markdown
## What's happening
One short paragraph describing what a user sees or what is going wrong.
No code, no jargon, no internal terms.

## Why it matters
One paragraph in the user's or customer's terms. Why do it now? If it is a
small polish item, say so.

## What we'll change
A short numbered list (2-4 items) of the visible changes. Plain language.

## How we'll know it's working
A short bulleted list of checks a non-technical person could do.

## Related
- Links to related tickets, the Product Discovery idea, Confluence pages,
  known issues. Leave the section out if there are none.

---

## The tech part

### Current state
- Full file paths with line numbers
  (`path/to/file.ext:10-15`).
- A 5-15 line excerpt of the real code, in a fenced code block.
- One sentence on why this code causes the problem.

### Fix
- Numbered steps describing the change.
- A code snippet of the fix shape, if it helps. Real-looking code, not
  pseudocode.
- Dependencies: other tickets, data changes, environment settings.

### Tests / verification
- Tests to add (unit, feature, component).
- For API responses: check the actual value of each field, not only that the
  field exists.
- Manual checks where automated tests are not practical.
- If a non-technical tester will check it, write the steps in user terms: no
  browser dev tools, no command line.

### Workflow notes
- Branch from the main branch, named `{KEY}-short-title-in-kebab-case`.
- No direct pushes to protected branches; pull request only.
- Wait until the deploy of this exact change has finished before moving the
  ticket, assigning the tester, or commenting "on staging".
- Note any cross-project order or rollback plan.
```

Match the length to the work. The template is a maximum, not a minimum. For a
one-line fix, "Current state" and "Fix" are a few sentences each.

## "What's happening": three tests

Keep it under 30 words, and check it three ways:

1. **Only what happens.** The present state and its effect. No
   recommendation; "should live in" or "needs to move" belong under "What
   we'll change".
2. **Drop numbers that change no decision.** "400 affected rows" sounds big
   but changes nothing the reader will do. The cause does.
3. **Name the trigger and the loss.** What starts the problem, and what is
   lost each time.

Example:

- Rejected: "The nightly export has failed 47 times since the last update,
  across 12 reports." (counts nobody acts on)
- Rejected: "The nightly export fails. It should retry when the file server
  is busy." (that is the fix, not the problem)
- Accepted: "When the file server is busy at night, the export stops and does
  not try again. The morning reports are then empty until someone reruns it
  by hand."

## Tone

- Top half: plain words a non-technical person understands. Speak about real
  users' time and trust. Be honest about size; do not oversell polish as
  critical. Avoid inside words ("dogfood" becomes "internal team testing";
  "happy path" becomes what actually happens).
- Tech half: be exact. Real file paths, line numbers, function names, quoted
  code. Never paraphrase code; quote it.

## Summary (the ticket title)

- 5-8 words, short and specific. Longer only when needed to be clear.
- **A bug** names the symptom, never the guessed cause.
  Good: "Signup form accepts phone numbers the server rejects."
- **A piece of work** names what must be done, as an instruction.
  Good: "Retry the nightly export when the file server is busy."
  Bad: "The nightly export sometimes fails." (describes, does not ask for
  anything)

## Issue type

Use the project's own types (`getJiraProjectIssueTypesMetadata`). Usually:
Bug for something broken, Task or Story for new work, Epic for a group of
tickets. Ask if the project uses unusual types.

## Priority

- **High:** users are blocked, data or security risk, or it blocks other
  work in progress.
- **Medium:** the default. A useful improvement or a bug with a workaround.
- **Low:** polish, clean-up, wording, developer comfort.

Some sites use other priority names. If "Medium" is rejected, read the
allowed values from the field metadata and map to the closest one.

## Plain text in Jira

Jira's default font shows many symbols as empty boxes. Use plain text:

| Avoid | Use instead |
| --- | --- |
| check-box symbols | `[ ]` |
| arrow symbols | `->`, "to", or a full word |
| warning emoji | no emoji; bold the word ("**Note:**") |
| greater-or-equal symbols | `>=` / `<=` |
| long or short dashes | a plain hyphen, or rewrite the sentence |
