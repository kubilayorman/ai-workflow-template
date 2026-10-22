# /feature test

## When this runs

Invoked as `/feature test`, after `/feature implement` and the user's hand check. Writes tests on the feature branch and runs the suite.

## Before running anything

Do *Every action* in `SKILL.md`: spec from the branch, read the rules, state the mode. Then check the spec's **Status**:

- `New` → stop: not built yet. Point to `/feature implement`.
- `Implemented` → do all the steps below.
- `Tested` → tests already exist (e.g. `/feature finalize` sent the user back after bringing in teammates' work). Skip to step 4, unless the user asks for more tests.
- `PR created` or `Documented` → stop and point to Part 2 of `claude-context/project-git-workflow.md`.

Also read the code the feature changed (`git diff origin/main...HEAD`), `tests/README.md` if there is one, and the existing tests.

## Steps

1. **Pick what's worth testing:** only parts where a mistake could slip through unnoticed — not code that only passes data along, sets up config or layout, or calls a library. Use only the layers under *Tests* in `CLAUDE.md`:

   - **Unit** (default): business rules, calculations, validation, permissions, status changes, edge cases, error handling.
   - **Integration:** only for a new or changed API endpoint, database query or outside-service call.
   - **End-to-end:** only for a new or changed core user flow from `docs/architecture.md`; one test per flow, main successful path.

   The spec's test notes are a starting point, not a must-do list. If nothing is worth testing, say so and propose no tests.

2. **Show the test plan and wait for approval** (stop, both modes), in plain language a non-technical reader can follow:

   | # | Layer | What we check | Why it matters | Example |
   |---|---|---|---|---|
   | 1 | Unit | An expired reset link is rejected | Old links shouldn't work forever | A link from 2 days ago shows "Link expired" |
   | 2 | End-to-end | A user can reset their password | It's how locked-out users get back in | Request a link, open it, set a new password, sign in |

   Below it, list in one line each what you chose not to test, and why, and say which file each test goes in. Apply the user's changes to the plan before writing any test.

3. **Write the tests** where *Tests* in `CLAUDE.md` and `tests/README.md` say, with their naming, helpers and fixtures (otherwise follow the existing tests, and mention it). Tests never call outside services for real.

4. **Run the tests.**
   - Start any local services under *Commands* in `CLAUDE.md`. If there's a *Database migrations* section, apply all migrations.
   - Run every test command under *Checks* in `CLAUDE.md` (unit and integration, then end-to-end if listed) — the whole suite, not only the new tests.

   If a test fails, work out whether the test or the code is wrong (fixing the code is allowed: it's a bug the test found). Explain and fix per *Modes*, rerun the whole suite, and repeat until everything passes. Never change a correct test to make it pass.

5. **Record the result in the spec.** Set **Status** to `Tested` and fill in **Test results** with the counts from the last run of each command and the new test files, e.g. `42 passed (5 new, in tests/unit/test_password_reset.py); end-to-end: 3 passed (1 new, in tests/e2e/password_reset.spec.ts)`.

6. **Commit** with the *Commit routine*.

7. **Stop.** Tell the user the tests pass and `/feature finalize` is next. Don't push or open a PR.
