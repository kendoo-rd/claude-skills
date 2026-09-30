# Finding related items

Do this before drafting. Pick 2-4 keywords from the request (the feature name,
the screen, the error text). Run the searches, then show the user one short
list: what was found, with a link and one line on why it matters.

Search results are often too large for one tool result; see `tool-fixes.md`
for how to read them.

## 1. Duplicates and related tickets

```
project = {KEY} AND statusCategory != Done
AND (summary ~ "keyword" OR description ~ "keyword")
ORDER BY updated DESC
```

- Read the top 10. A near-match means: suggest a comment or update on that
  ticket instead of a new one.
- Also run it once without the status filter. A recently closed ticket for
  the same problem means it came back; link it as "relates to" and say so.

## 2. The right epic

```
project = {KEY} AND issuetype = Epic AND statusCategory != Done
ORDER BY updated DESC
```

Match on the epic name and description. Suggest the best one with a reason.
If nothing fits, use the project's `default_epic`, or offer a new epic when
the work is big.

## 3. Product Discovery ideas

Only when `discovery_project` is set.

```
project = {DISCOVERY_KEY} AND (summary ~ "keyword" OR description ~ "keyword")
```

If an idea matches, suggest linking the ticket to it. Check `getIssueLinkTypes`:
Product Discovery usually adds its own delivery link type; use it if present,
otherwise "Relates".
This shows product people that the idea is being delivered.

## 4. Confluence pages

Use `searchConfluenceUsingCql`:

```
type = page AND text ~ "keyword" AND space in ("<SPACE-KEY>")
```

Follow `confluence_spaces` in the settings: `null` means skip this search;
a list means keep the `space in (...)` part with those keys; `"all"` means
drop the `space` part. Suggest the 1-3 most
useful pages (specs, how-to pages, decision notes). Add them to the ticket as
links in the description under "Related".

## 5. Known issues in Service Management

Only when `service_desk_project` is set. Known issues usually live as
"Problem" tickets, or as tickets with a known-error label.

```
project = {DESK_KEY}
AND (issuetype = Problem OR labels in (known-error, known-issue))
AND (summary ~ "keyword" OR description ~ "keyword")
```

If the issue type does not exist in that project, drop that part and search
by text only. A match means customers already hit this. Link it, and say so
in "Why it matters".

## Linking

- Use `createIssueLink` with a type from `getIssueLinkTypes` ("Relates",
  "Blocks", "Duplicate").
- Confluence pages go in the description as links.
- Ask before linking, as part of the draft. Never link on a guess.
