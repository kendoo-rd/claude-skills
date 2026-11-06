# The ticket

Two clearly separated halves. The top half is for everyone and is required
on every ticket. The bottom half, The tech part, is for the person doing the
work. It is optional: include it when there is technical detail to hand over.

The headings always appear in this order: What's happening, Why it matters,
What we'll change, How we'll know it's working, Not in this task,
Related / Reference, then The tech part.

For work with no code (marketing, operations, support), keep the top half and
replace the tech half with "Details": the steps, the people involved, and the
files or pages needed.

## Template

```markdown
## What's happening
One short paragraph describing what a user sees or what is going wrong.
No code, no technical terms, no inside words.

## Why it matters
One paragraph in the user's or customer's terms. Why do it now? If it is a
small polish item, say so.

## What we'll change
A short numbered list (2-4 items) of the visible changes. Business
language.

## How we'll know it's working
A short bulleted list of checks a non-technical person could do.

## Not in this task
What people will ask about and should not expect here. One line per item.
Name the ticket that covers it, if one exists. Leave the section out if
there is nothing to exclude.

## Related / Reference
- Links to related tickets, the Product Discovery idea, Confluence pages,
  known issues. One link per bullet, with why it matters. Leave the section
  out if there are none.

---

## The tech part

Optional. Include it when there is technical detail to hand over; leave it
out when there is none.

### Current state

Keep facts and guesses apart. A good ticket does not need a proven cause.

- **Observed:** what was seen, and where (steps, error text, screen, logs).
  When the code was checked: full file paths with line numbers
  (`path/to/file.ext:10-15`) and a 5-15 line excerpt of the real code.
- **Cause:** either "Confirmed:" with one sentence on why this code causes
  the problem, or "Suspected:" with the likely cause and what points to it.
  Never state a guess as a fact.
- **To investigate** (only when the cause is not confirmed): the checks that
  would confirm it, for example reproduce with these steps, read these logs,
  compare these settings.

### Fix

When the cause is only suspected, write the fix as a proposal, or leave this
section as "After investigation".

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

## Language

- Top half: business language at B2-C1 level. Clear and professional, not
  simplified. Product and business terms are fine: onboarding, churn,
  conversion, invoice run. No technical terms: file names, endpoints, table
  names and error traces go in the tech part. Avoid inside words ("dogfood"
  becomes "internal team testing"; "happy path" becomes what actually
  happens). Speak about real users' time and trust. Be honest about size; do
  not oversell polish as critical.
- Tech part: technical terms are welcome here. Be exact. Real file paths,
  line numbers, function names, quoted code. Never paraphrase code; quote it.

## Summary (the ticket title)

- 5-10 words, short and specific. Longer only when needed to be clear.
- Business language a new colleague would understand. No technical terms.
- **A bug** names the problem the user sees, never the guessed cause.
  Good: "Signup form accepts phone numbers the server rejects."
  Bad: "Missing phone validation in signup handler."
- **A story** names what the customer will be able to do, as an instruction.
  Good: "Let customers download an invoice as PDF."
- **A task** names the result of the work, as an instruction.
  Good: "Retry the nightly export when the file server is busy."
  Bad: "The nightly export sometimes fails." (describes, does not ask for
  anything)
- **An epic** names a goal that can finish.
  Good: "Invoices customers can download and forward."
  Bad: "Invoice work." (a topic, not a goal)
- No ticket keys, dates, names of people or inside words in a title.

## Issue type

Use the project's own types (`getJiraProjectIssueTypesMetadata`). Usually:
Bug for something broken, Task or Story for new work, Epic for a group of
tickets. Ask if the project uses unusual types.

## Epics

An epic uses the same format as any ticket: the same title rule, the same
required top half, the same optional tech part (only when there is order or
dependency detail to hand over). What differs: it names a goal, and its
child tickets deliver it. The six headings read like this for an epic:

| Heading | For an epic |
| --- | --- |
| What's happening | The gap today, in the customer's words. |
| Why it matters | Why now: a customer need, a risk, a deadline. |
| What we'll change | The children, as results, in order. |
| How we'll know it's working | Two to four checks that show the goal is reached. |
| Not in this task | What people will ask about and should not expect from this epic. |
| Related / Reference | The idea it came from, decisions, customer reports. |

- Open an epic when several tickets are needed to reach one goal. Reuse an
  open epic if one fits.
- Three to eight children. Fewer is one story; more is two epics.
- Each child is useful and testable on its own. Split by what the user gets,
  not by layer or team.
- No bucket epics ("Tech debt", "Q1 improvements" never finish). No children
  named after a layer ("Backend", "QA", "Part 1 of 3").
- When two children depend on each other, say which ships first.
- Draft the epic and all its children together; review them before creating
  any.

## Priority

- **High:** users are blocked, data or security risk, or it blocks other
  work in progress.
- **Medium:** the default. A useful improvement or a bug with a workaround.
- **Low:** a small improvement that blocks nobody and no other work: polish,
  clean-up, wording, developer comfort.
- Never set High to get attention. When everything is High, nothing is.

Some sites use other priority names. If "Medium" is rejected, read the
allowed values from the field metadata and map to the closest one.

## Labels

- Use labels from the team's shared list (the settings file names it, if the
  project has one). Short, lowercase, words joined with hyphens.
- Do not invent a label that only one person uses.
- Link only what really relates, and say why the link matters.
- Never write a password, token or key. Point to where it lives instead.

## Plain text in Jira

Jira's default font shows many symbols as empty boxes. Use plain text:

| Avoid | Use instead |
| --- | --- |
| check-box symbols | `[ ]` |
| arrow symbols | "to", or a full word |
| warning or status emoji | no emoji; a plain label ("**Note:**") |
| greater-or-equal, less-or-equal symbols | "at least", "at most" |
| long or short dashes | a plain hyphen, or rewrite the sentence |

## Before you create

Run through this before showing the final draft.

- Type matches what the work is, not its size.
- Title: 5-10 words, follows the rule for its type, no keys, dates, names or
  inside words.
- Top half: business language, B2-C1, no technical terms, no inside words.
- "What's happening" describes the present state only, under 30 words.
- "Why it matters" is in customer or business terms.
- "What we'll change" is a numbered list of visible changes.
- "How we'll know it's working" has checks a non-technical person could run.
- "Not in this task" names what people will ask about.
- "Related / Reference" has one link per bullet, each with a reason.
- Tech part left out when there is nothing technical to hand over; cause
  labelled Confirmed or Suspected; fix marked Proposed when only suspected.
- Epic: same format as every ticket, goal can finish, three to eight
  children each useful on its own, release order stated where it matters.
- Priority Medium unless there is a reason. Words, not symbols. No secrets.
