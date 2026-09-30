# Comments

## The rule: short and plain

A comment is a status note, not a report. One or two sentences anyone can
understand.

- No file paths, function names, commit hashes, database queries or jargon.
  Those belong in the description, not the comment thread.
- Say what changed and where it stands. A link to the pull request is fine;
  explaining the code is not.
- If you are writing a third sentence or pasting a code word, cut it.

Bad: "Fixed, the email check in the signup handler now trims spaces (see the
pull request and the commit)."

Good: "Fixed and merged. This is now live on staging."

## Written as the signed-in user

The connector posts comments as whoever is signed in. Write in first person
("I checked", "I'll look at it"). Never write about that same person in the
third person.

## Progress comments

Post one comment at each step, when it happens. Do not save several steps
for one late comment.

| Moment | Comment |
| --- | --- |
| Pull request opened | "The fix is ready and in review." (add the link) |
| Deployed to a test environment | "This is now on staging and ready to test." |
| Merged, waiting for production | "Merged, waiting to go live." |
| Live in production | "This is now live." |

Post the staging or production line only after the deploy of that exact
change has finished. Adapt the environment names to the team's own.

Ask before posting, unless the user already said to comment at each step for
this ticket (see the hard rules in `SKILL.md`). Keep the comment id each post
returns, so you can correct that comment instead of adding a new one.

## Mentioning people

Any time a comment or description refers to a teammate (assigning, asking,
thanking, handing over), use a real @-mention so they get notified. A typed
name does nothing.

1. Find the account id with `lookupJiraAccountId`.
2. Post the comment with `contentFormat: "adf"` and a `mention` node that
   carries the account id. Markdown mentions (`[~accountid:<id>]`) do not
   always turn into real mentions, so use ADF from the start whenever a
   mention matters.

   ```json
   {"type": "doc", "version": 1, "content": [{"type": "paragraph", "content": [
     {"type": "mention", "attrs": {"id": "<account-id>"}},
     {"type": "text", "text": " this is ready for you to test."}
   ]}]}
   ```

3. If the mention still shows as plain text, update **the same comment**
   (pass its `commentId`). Never post a second comment to fix the first. If
   your connector cannot update comments, tell the user and let them fix it
   in Jira.
