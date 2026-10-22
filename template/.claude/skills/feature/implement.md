# /feature implement

## When this runs

Invoked as `/feature implement @<spec-file>`, e.g. `/feature implement @claude-context/12-password-reset-spec.md`. The argument names the spec to build.

## Steps

1. **Find the spec.** Use the file named in the argument.
   - No argument, or the file doesn't exist: say so, list the `claude-context/*-spec.md` files whose **Status** is `New`, and ask which to build. Don't pick one yourself.
   - **Status** isn't `New`: stop, the feature is already built. Give the next step (`Implemented` → `/feature test`, `Tested` → `/feature finalize`, `PR created` or `Documented` → already finalized).

   Then do *Every action* in `SKILL.md`: read the rules, state the mode.

2. **Check the starting point.** Run `git status`. Every feature starts on `main`. If you're not on `main`, or anything besides the spec is uncommitted, stop and ask.

3. **Read the code the spec touches.** The code is always the source of truth.
   - `docs/architecture.md` and `claude-context/Project Overview/` help with orientation, but can lag. Don't stop because they differ from the code or spec.
   - If the feature adds or changes anything the user sees, open every image in `claude-context/ui-prototypes/` (there are only a few).

4. **Build it**, following `ai-interaction.md`.
   - **Branch first**, from an up-to-date `main` (steps 2–3 of `claude-context/project-git-workflow.md`): `git pull --ff-only`, then `git checkout -b <spec slug>` (e.g. `12-password-reset`). If that branch already exists, stop: a feature is built once, on a new branch.
   - **Follow existing code patterns.** Schema changes go through the migration tool, per *Database migrations* in `CLAUDE.md`.
   - **UI follows `claude-context/ui-prototypes/`** closely, not pixel for pixel. A screen with a matching screenshot (named in the spec, or by file name) follows it; others take their look from all of them. Ask first if matching needs a new dependency or big structural change.
   - **No automated tests,** even if the spec lists some. That's `/feature test`.
   - **Explain what changed** in plain language (`ai-interaction.md`), with one line per new or changed screen on how it follows the screenshots, and end with the spec's **How to see it working:** line.
   - **Before the first commit,** set the spec's **Status** to `Implemented`, so it goes onto the branch marked as built.
   - **Commit** on the feature branch with the *Commit routine*, as often as makes sense.

5. **Everything else in `ai-interaction.md` applies:** ask before big structural decisions, add nothing the spec didn't ask for, and mention off-task cleanup ideas instead of doing them.

6. **Stop after the commit.** Don't write tests, push, open a PR or merge.

   If the user reports a problem while trying the feature, fix it on this branch, explain the fix, and commit it the same way. When they're happy, tell them `/feature test` is next.
