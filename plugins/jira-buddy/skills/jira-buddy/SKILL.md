---
name: jira-buddy
description: |
  Use whenever the user wants to work with Jira tickets: file a bug,
  improvement or feature ("file a ticket for...", "let's track this", "add a
  Jira task"), update an existing ticket (status, labels, assignee, priority),
  post a progress comment ("comment that it's on staging", "mark it live"), or
  break a big request into an epic with smaller tickets. Also use when the
  user hands over a problem without asking for it to be fixed now. Finds
  related tickets, the right epic, matching Product Discovery ideas,
  Confluence pages and known issues in Jira Service Management before
  drafting. Needs the Atlassian connector (MCP) for Claude.
---

# jira-buddy

Turn a short problem note into a clear, ready-to-pick-up Jira ticket, keep it
connected to the work around it, and keep it updated as the work moves.

Side files in this folder, read them when the step needs them:

- `setup.md` - first-run setup and the settings file.
- `related-items.md` - how to find related tickets, epics, ideas, docs and
  known issues.
- `ticket-template.md` - the two-part ticket, summary rules, priority guide.
- `comments.md` - progress comments and @-mentions.
- `tool-fixes.md` - known Atlassian connector problems and their fixes. Read
  it before the first create, edit, search, label or status call.

## Before anything: check the connector

This skill needs the Atlassian connector (MCP). Tool names and features
change between connector versions, so look at the tools you actually have
and match them by purpose, not by exact name:

| Purpose | Name in the version this was tested with |
| --- | --- |
| List sites | `getAccessibleAtlassianResources` |
| Search tickets | `searchJiraIssuesUsingJql` |
| Read / create / edit a ticket | `getJiraIssue`, `createJiraIssue`, `editJiraIssue` |
| Move status | `getTransitionsForJiraIssue`, `transitionJiraIssue` |
| Add or update a comment | `addCommentToJiraIssue` (with `commentId` to update) |
| Link tickets | `createIssueLink`, `getIssueLinkTypes` |
| Required fields | `getJiraIssueTypeMetaWithFields` |
| Find a person | `lookupJiraAccountId` |
| Search Confluence | `searchConfluenceUsingCql` |

Read each tool's own description before first use; it beats anything
written here. If there are no Atlassian tools at all, tell the user in one
line that the connector must be added and signed in, then stop. Do not fake
a ticket in a local file instead. If one feature is missing (for example
attachments), say so and offer the manual step.

## What the user can ask for

| Request | Flow |
| --- | --- |
| "File a ticket for X", "X is broken", "let's improve Y" | New ticket |
| "Split this into tickets", a request with many parts | Big work |
| "Move this ticket to done", "add a label", "assign it to someone" | Update ticket |
| "Comment that it's live", PR opened, deploy finished | Progress comment |

## Route by intent first

Decide which of the four requests above this is, then follow only that path.
Research and full drafts are for new work; a label change does not need a
duplicate search.

Every path starts by loading settings: read `.claude/jira-buddy.json` in the
project. If it is missing, run first-time setup (`setup.md`) and come back.
(For a single update or comment on a ticket the user named by key, only the
site is needed; if settings are missing, find the site and skip the rest of
setup.)

## New ticket

1. **Load settings** (above).
2. **Find related items** (`related-items.md`). Always look for duplicates and
   the right epic. Also look in Product Discovery, Confluence and Service
   Management when the settings list them. Show what you found in a short
   list with links. If a near-duplicate exists, suggest updating or commenting
   on it instead of opening a new ticket.
3. **Look at the code first**, when the work is in an open code project.
   Find the real files, line numbers and most likely cause. Use a read-only
   search helper (such as the `Explore` agent) with a focused prompt, and cap
   its answer at 300-400 words. Never change code in this step. Skip this step
   when there is no code (marketing, HR, support, and so on).
4. **Ask what is still unclear.** One topic at a time. Offer choices when the
   answers are predictable. If the user says "decide for me", pick one and
   give the reason in one sentence.
5. **Show the draft.** Include: project, issue type, parent epic, summary,
   full description, priority, labels, links to related items, and a
   suggested assignee with a one-line reason (see "Assignee" below). For big
   work, show the epic plus every child ticket.
   Before showing the draft, check the project's required fields for that
   issue type (`getJiraIssueTypeMetaWithFields`) and fill them, so creation
   does not fail halfway.
6. **Create it only after a yes.** Then add the links, and give the user a
   clickable link for every ticket touched.
7. **Narrate delivery later** with one-sentence comments (`comments.md`).

For a new ticket, never skip step 2 or step 5. A vague ticket gets bounced
back; a concrete one saves the next person hours.

## Big work: one epic, small tickets

Follow the "New ticket" steps, with these additions. When a request has
several parts or spans more than one team:

- Suggest an existing epic if one fits; otherwise draft a new epic.
- Split into 3-8 child tickets. Each one must be deliverable on its own.
- If the work spans two code projects (for example the server sends a new
  field and the app shows it), make one ticket per project, cross-link them,
  write "Paired with {OTHER-KEY}" in both, and state the order. The one that
  ships first must not break anything while the other is pending.
- Show the whole tree as one draft, with required fields checked for every
  issue type used. Create the epic first, then the children with `parent` set
  to the epic key.

**Many writes can fail halfway.** Protect against duplicates:

1. Keep a running list as you go: each draft item and the key Jira returned
   for it. Show it to the user at the end.
2. If a call fails or times out, stop. Do not retry blindly: the ticket may
   have been created even though the call reported an error.
3. Check first: search for the summary under the same parent
   (`parent = {EPIC-KEY} AND summary ~ "..."`), or read the epic's children.
4. Create only what is really missing. Then continue with the rest.
5. If you cannot finish, tell the user exactly what was created (with links)
   and what was not.

## Update ticket (short path)

1. **Read the ticket** the user named (`getJiraIssue`). If they did not name
   one, find it with a quick search and confirm which one.
2. **Check the change is valid** with only the checks below that apply.
3. **Show the change** in one or two lines ("KEY-12: status In Progress ->
   Done, add label `needs-review`") and wait for a yes.
4. **Apply it** and give the link.

No duplicate search, no epic search, no full draft.

- **Status:** call `getTransitionsForJiraIssue` for that ticket first. Never
  reuse a transition id from memory or from another project.
- **Labels:** read the current labels, merge, then write the full list back.
  Setting labels replaces them all.
- **Assignee:** look up the account id with `lookupJiraAccountId`.
- **Description:** show the changed part before saving.

## Progress comment (short path)

Write the one-sentence comment (`comments.md`), show it, post after a yes,
and give the link. If the user already approved comments at each step for
this ticket, post without asking again.

## Assignee

Leave the ticket unassigned unless the user or the settings name someone. But
always suggest a person with the draft, with a one-line reason based on
evidence: who raised it, who owns the ticket this blocks, who works in that
area day to day. When two people fit, show both and recommend one. Set it only
after the user agrees.

## Hard rules

- **Nothing is created, changed or commented without a yes.** A yes covers
  what was shown. One exception: if the user says to post progress comments
  at each step for a ticket, that yes covers those comments until the work
  on that ticket ends or the user says stop.
- **Search for duplicates before drafting a new ticket.**
- **No secrets.** Never put passwords, tokens or API keys in a ticket or
  comment. Point to where they live instead ("see the password manager").
- **Plain text only in Jira.** No arrows, emoji, check-box symbols or special
  dashes; Jira's default font shows many of them as empty boxes. See the table
  in `ticket-template.md`.
- **Comments are posted as the signed-in user.** Write in first person ("I
  checked"), never about that user in the third person.
- **Real @-mentions, never typed names** (`comments.md`).

Rules for code work, used only when the ticket leads to a code change:

- **Ticket before branch.** Every code change needs a ticket first, even small
  fixes. The branch is named after the ticket key, so the ticket must exist.
- **No local ticket files.** Do not save `{KEY}.md` or any copy of the ticket
  in the project. The Jira description is the handoff; write it complete
  enough that a fresh session can do the work from the ticket alone.
- **Ticket keys in PRs and commits link automatically.** Write another
  ticket's bare key only when you want that link (see `tool-fixes.md`).

## When to ask and when to act

| Situation | Default |
| --- | --- |
| Clear issue, code confirms the cause | Draft, then ask to confirm |
| Cause not confirmed (no access, cannot reproduce) | File it anyway, with the cause marked as suspected |
| Several possible causes | Look further, then ask one question |
| User says "decide for me" | Pick and explain in one line |
| Near-duplicate found | Suggest updating it instead |
| Fix is only a settings or environment change | Say so; no code ticket needed |
| Work spans two code projects | Split into paired tickets |
| Anything that deletes data or touches production | Ask first, every time |

A tip for code work: if production works and a test environment does not, on
the same code, the cause is almost always environment settings, not code.
Check those first.

## What this skill does not do

- Write or change code. Filing is filing; the work happens later, from the
  ticket.
- Send email or chat messages.
- Anything your connector version does not offer (for example attachments or
  deleting comments). Check the tool list; if it is missing, tell the user
  and give them the manual step.

## Worked example

User: "The signup form lets me type letters in the phone number, but saving
fails with an error."

1. Settings found; the default project and its signup epic.
2. Related search: no duplicate; one Confluence page on form rules.
3. Code check: the phone field has no format check; the server rejects
   anything that is not digits.
4. No open questions.
5. Draft: summary "Signup form accepts phone numbers the server rejects";
   plain half says the user fills the whole form and sees an error only after
   saving; tech half quotes both files and lines and suggests the same check
   on the form. Priority Medium. Suggested assignee: the person who last
   worked on the form, with the reason. Link to the Confluence page.
6. User says yes. Ticket created under the epic, page linked, link shared.
