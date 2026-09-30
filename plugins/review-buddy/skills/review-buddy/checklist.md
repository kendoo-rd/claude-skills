# The five passes

Use what applies to the change. A pass with nothing to report is skipped, not
padded. Each item lists what to look for; a finding still needs file:line and
a concrete case where it goes wrong.

## Changes with little or no code

For changes that are only settings, docs or data files (JSON, YAML, Markdown,
infrastructure or CI config), all five passes still apply. Adapt passes 2
and 3 to what a settings change can break:

**Correctness for settings**
- The files parse and follow the schema the tool expects.
- New entries match their neighbours: same fields, same naming, paths and
  names that exist.
- Values are right for each environment; defaults are safe when a value is
  missing; nothing that was set before is silently dropped.
- Anything that reads these files (build, deploy, CI, loaders, feature
  flags) still behaves the same, or changes on purpose.

**Security for settings**
- Checks turned off: certificate or signature checks, authentication, rate
  limits, debug or verbose error modes switched on.
- Permissions widened: roles, access policies, public buckets, open network
  rules, broader CORS, new admin rights.
- Secrets, private addresses or personal data added in plain text.

**Performance and cost for settings**
- Resource limits, timeouts, pool sizes, retries, cache lifetimes, instance
  sizes: realistic, and not able to cause runaway cost.

**Docs**
- Commands, file names, options and examples match what the code or tool
  really does.

## 1. The ask

- Each acceptance point: done, partly done, or missing.
- Extra changes nobody asked for (unrelated refactors, new features, config
  changes). Not always wrong, but call them out: they make the change harder
  to review and to undo.
- Behavior the ask implies but does not state (for example an "edit" feature
  also needs a permission check and an error message).

## 2. Correctness

**Logic**
- Edge cases: empty lists, zero, negative numbers, null or missing values,
  very long strings, time zones, leap years, first and last item.
- Off-by-one errors in loops, ranges and paging.
- Wrong comparisons (`=` vs `==`, loose vs strict equality, comparing
  objects by reference).
- Copy-paste mistakes: a variable not renamed in the copied block.
- Changed behavior for existing callers that were not updated.

**Error handling**
- Errors swallowed silently (empty `catch`, ignored return codes, `except:
  pass`).
- Errors caught at the wrong level: too early to handle well, or never
  caught.
- Messages that help nobody ("Error occurred") or that leak internals to end
  users.
- Clean-up on failure: open transactions rolled back, partial writes undone,
  temporary files removed.
- Retries without a limit or without a delay.

**Race conditions**
- Shared data changed from more than one thread, request or async task
  without a lock or atomic operation.
- Check-then-act gaps (check a record exists, then create it) without a
  unique constraint or transaction.
- Double submits: a button or endpoint that can run twice and create two
  records or two charges.
- Async code: missing `await`, promises not handled, callbacks that run after
  the component or request is gone.

**Memory and resource leaks**
- Files, database connections, sockets or streams opened without being
  closed (use `with`, `try/finally`, `using`, `defer`).
- Event listeners, subscriptions, timers and intervals added without being
  removed.
- Caches, maps or lists that only grow.
- Large objects kept alive by closures or global state.

## 3. Design, security and performance

**Design patterns and best practices**

Design points are findings only with evidence: a concrete consequence (a bug
it causes or invites, a change it makes much harder, a test it prevents) or
a written project rule it breaks. Taste alone is not a finding. At most a
Nit, and only when the benefit is clear.

- The right tool for the job. An extra layer, factory or interface with one
  user is a finding only when it causes a real cost, for example the same
  change now has to be made in three places.
- Functions or classes that mix unrelated work (fetch data, format it and
  send email) are a finding when that mixing causes a problem: it cannot be
  tested, a failure in one part breaks another, or the codebase has a rule
  against it.
- Logic in the wrong layer (database queries in a view, business rules in a
  controller) when the codebase clearly separates them; name the files that
  show the convention.
- Best practices for the language and framework in use (for example
  parameterized queries, framework validation helpers, the framework's own
  way of handling dates or money).

**Security**
- Unsafe input: user input reaching a database query, shell command, file
  path, HTML output, redirect or template without escaping or validation.
- Missing permission checks: can one user read or change another user's
  data by changing an id?
- Secrets: keys, passwords or tokens in code, config files, logs or error
  messages.
- Weak defaults: debug mode on, open CORS, disabled certificate checks.
- Sensitive data stored or logged in plain text.

**Performance**
- Runtime complexity: nested loops over data that can grow, repeated
  searches in a list that could be a set or map.
- Database or network calls inside a loop (the "N+1" problem); missing
  indexes for new queries on large tables.
- Loading everything when only a count, page or single field is needed.
- Heavy work on a hot path (every request, every render, every keystroke)
  that could be cached, batched or moved.
- Only flag what matters at the data sizes this code will really see.

**Breaking changes**
- Public functions, API endpoints, events or file formats whose shape
  changed: who else uses them?
- Database changes: are they safe on existing data? Is there a way back?
- Renamed or removed settings and environment variables.
- Old data, old clients or cached content that will meet the new code.

**New dependencies**
- Is the library needed, or does the codebase or standard library already do
  this?
- Is it maintained (recent releases, open issues handled)?
- Known security problems, and a license that fits the project.
- Is the version pinned the way the project pins others?

## 4. Fits the codebase

Learn the conventions from the project's rules and from two or three nearby
files that do similar things, then compare.

**Coding conventions**
- Naming (files, classes, functions, variables), formatting, folder layout,
  import order, the way tests are named and placed.
- Rules in the project's linter, formatter and `CLAUDE.md` or
  `CONTRIBUTING.md`.

**Existing patterns**
- A new helper that duplicates an existing one. Name the existing one.
- A different way of doing a common thing (errors, logging, config, data
  access, API calls) than the rest of the codebase.
- A break from the convention that is clearly better: note it, do not block
  it, and suggest applying it consistently later. A break for the worse is a
  finding.

**Readability**
- Clear names that say what things are and do.
- Dead code, commented-out code, leftover debug prints.
- Comments where the code is not obvious (why, not what); no comments that
  repeat the code.

**Logs**
- Useful logs at the right level for new failure paths.
- No secrets or personal data in logs.

## 5. Tests

- New behavior has tests, including error paths and edge cases from pass 2.
- The tests would fail if the change were removed. A test that passes
  either way tests nothing.
- Assertions check real values, not only that something exists or did not
  crash.
- No tests weakened, skipped or deleted to make the change pass. If one was,
  it must be explained.
- Tests follow the project's test style and location.
- Flaky patterns: fixed sleeps, real network calls, dependence on test order
  or the current date.
