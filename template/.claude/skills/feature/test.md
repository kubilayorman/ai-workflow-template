# /feature test

## When this runs

Invoked as `/feature test`, after `/feature implement` has finished and
the user has tried the feature by hand. Writes tests for the feature on
its branch (unit tests by default; integration or end-to-end tests where
the feature calls for them) and runs the test suite. This is the only
`/feature` action that writes or runs tests.

## Before running anything

Check the current branch. If it is `main`, stop: there is no feature
branch to test. The spec is `claude-context/<current-branch>-spec.md`.
If it doesn't exist, stop and ask. Then look at its **Status**:

- `New` → stop: the feature hasn't been built yet. Point the user to
  `/feature implement`.
- `Implemented` → go through all the steps below.
- `Tested` → the tests are already written (e.g. `/feature finalize`
  sent the user back here after bringing in teammates' changes). Skip to
  step 4 and run them, unless the user asks for more tests.
- `PR created` or `Documented` → stop and point the user to Part 2 of
  `claude-context/project-git-workflow.md`.

Read the spec's **Mode** line and state it in one line (e.g. "Mode:
SURF"). No **Mode** line means `VERBOSE`. See *Modes* in `SKILL.md`.

Read `CLAUDE.md`, `claude-context/ai-interaction.md`, the spec, the
code the feature added or changed (`git diff origin/main...HEAD`),
`tests/README.md` if there is one, and the project's existing tests, if
not already fresh in context. Every git
command is shown before it runs; in VERBOSE, wait for a go-ahead. Never
add a `Co-Authored-By` line for Claude or any AI tool.

## Steps

1. **Pick what's worth testing.** Only the parts of the feature where a
   mistake could slip through unnoticed. Skip code that only passes data
   along, sets up config or layout, or calls a framework or library
   without logic of its own. Pick the layer for each test, using only
   the layers listed under *Tests* in `CLAUDE.md`:
   - **Unit** (the default): business rules, calculations, validation,
     permissions, status changes, edge cases and error handling.
   - **Integration**: only if the feature adds or changes an API
     endpoint, a database query or a call to an outside service.
   - **End-to-end**: only if the feature adds or changes a core user flow
     from `docs/architecture.md`. One test per flow, covering the main
     successful path.

   The spec's test notes under *Feature technical requirements* are a
   starting point, not a must-do list. If nothing in the feature is
   worth testing, say so and propose no tests.
2. **Show the test plan and wait for approval.** A table with one row
   per test, in the plain-language style of `ai-interaction.md`, written
   so a non-technical reader can follow it:

   | # | Layer | What we check | Why it matters | Example |
   |---|---|---|---|---|
   | 1 | Unit | An expired reset link is rejected | Old links shouldn't work forever | A link from 2 days ago shows "Link expired" |
   | 2 | End-to-end | A user can reset their password | It's how locked-out users get back in | Request a link, open it, set a new password, sign in |

   Below the table, list in one line each what you chose not to test and
   why. Say which file each test goes in (see step 3). Don't write any
   test until the user approves; apply their changes to the plan first.
   This stop applies in both modes.
3. **Write the tests.** Put them where *Tests* in `CLAUDE.md` and
   `tests/README.md` say, following their naming, and reuse the shared
   helpers and fixtures they name. If they don't say, follow where the
   project's existing tests live, and mention it. Tests never call
   outside services for real.
4. **Run the tests.** Start any local services listed under *Commands*
   in `CLAUDE.md`. If the project has a *Database migrations* section,
   apply all migrations. Then run every test command under *Checks* in
   `CLAUDE.md` (unit and integration, then end-to-end if listed), the
   whole suite, not only the new tests.

   If a test fails, find out whether the test or the feature's code is
   wrong. In VERBOSE, explain it in plain language, propose the fix, and
   wait for a go-ahead. In SURF, fix it, then explain in plain language
   what was wrong and what you changed. Fixing the feature's code is allowed here: it's a bug the
   test found. After each fix, rerun the whole suite. Keep iterating
   until everything passes. Don't change a test just to make it pass
   when the test is right.
5. **Record the result in the spec.** Change **Status** to `Tested` and
   fill in **Test results**: the counts from the last run of each test
   command and the new test file(s), e.g. `42 passed (5 new, in
   tests/unit/test_password_reset.py); end-to-end: 3 passed (1 new, in
   tests/e2e/password_reset.spec.ts)`.
6. **Commit.** Run `git status`, check the file list against *Never
   commit* in `CLAUDE.md` (a hit always stops, in both modes), then show
   the exact `git add -A` and commit commands. In VERBOSE, wait for
   approval; in SURF, run them.
7. **Stop.** Tell the user the tests pass and that `/feature finalize`
   is next. Don't push or open a PR.
