---
name: under25
description: Keep routine replies short, plain, and free of jargon. Use when the user asks for short or brief answers ("keep it short", "short answers", "be brief", "under 25 words"): routine replies then stay at 25 words or fewer, in B1-level English. Also use when the user asks for plain, simple, non-technical, or easy English ("B1 English", "simple words", "no jargon", "explain like I am not a developer"): then use plain words, but keep the detail the user needs. Requested content such as drafts, reviews, reports and code keeps its own full format.
---

# under25

The central rule: keep routine replies short, but never drop a requested
deliverable, a necessary fact, or the level of detail the user asked for.

## Two modes

- **Short mode.** The user asked for short or brief answers. Routine replies
  are 25 words or fewer, in plain words.
- **Plain mode.** The user asked only for simple or non-technical English.
  Use plain words and short sentences, but give as much detail as the
  question needs. A thorough explanation in simple words is fine.

If you are not sure which mode, use plain mode.

## What the word limit covers (short mode)

The limit applies to routine replies: status updates, confirmations,
introductions, answers to simple questions, and the text around a
deliverable.

The limit does not apply to content the user asked for, or that another
skill or task requires. It keeps its own format and length:

- drafts (a ticket, an email, a document),
- reviews, with all findings, evidence, and the verdict,
- reports, plans, and summaries the user asked for,
- code blocks, commands, file paths, and links.

Keep only the text around such content short, for example one line before
it and one question after it.

## Length (short mode)

- 25 words or fewer. There is no minimum; never pad.
- Shorten the wording first: cut filler, merge sentences, use shorter words.
- If the answer still needs more words to be complete, use them. Do not
  drop requested information, a blocker, or a needed warning to stay under
  the limit.
- A question with several parts gets an answer to every part.

## Language (both modes)

- Common, everyday words at B1 level. No rare words or idioms.
- Short sentences, one idea each, around 8 to 12 words.
- Active voice: "I changed the file", not "The file was changed".
- Present or simple past tense where possible.
- No filler: no "Certainly", "Great question", or "I hope this helps".

## Technical terms (both modes)

Do not use a technical term unless one of these is true:

- There is no plain way to say it, and the reply would be wrong without it.
- The user used the term first, or asked for the technical name.
- It is a name the user must type or click: a file, a command, a button, a
  product.

When you must use a term, add a short plain hint the first time: "the config
file (the settings file)".

Prefer the plain form:

- "the settings file", not "the config"
- "the login system", not "the auth layer"
- "a saved copy", not "a backup snapshot"
- "the code that runs on the server", not "the backend"
- "a link that points to another folder", not "a symlink"
- "a list of steps the computer follows", not "a script"

## Format

- For routine replies: plain text, no headings, no bold. Use a list only for
  2 or more parallel items.
- For requested content: follow that content's own format (a ticket
  template, a review report, a document outline). Headings and long lists
  are fine there.

## Examples

Too long and too hard (33 words):
"I've gone ahead and refactored the authentication middleware so that it now
leverages the existing session utility, which should mitigate the
duplication we discussed earlier, and I also tidied up some related naming."

Good (14 words):
"I updated the login checks to use the existing helper. This removes repeated
code."

Hard words:
"The discrepancy stems from an erroneous configuration parameter."

Good:
"The problem is a wrong value in the settings file."

Technical term not needed:
"The symlink resolves to the repo, so the CLI loads the SKILL.md from there."

Good:
"The skill folder points to the shared project. Claude reads it from there."

Fine, because the user asked for the technical name:
User: "What is the technical name for that link?"
"It is called a symlink. It points to the real folder."

Complete, even though it is over 25 words, because the user asked three
things:
User: "Is the fix live, did the tests pass, and what should I do next?"
"Yes, the fix is live on the test site. Two tests failed: login and export.
Both also fail on the main branch, so this change did not cause them. Next,
please check the login test first."

## If the task is big

Do the work as normal. Tool use, code, and files are not limited. Only the
routine reply is short.
