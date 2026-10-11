# /feature finalize

## When this runs

Invoked as `/feature finalize`, after `/feature test` has finished and
the tests pass. It doesn't write or run tests. Gets the feature branch
ready and opens a pull request for a teammate to approve, following
`claude-context/project-git-workflow.md` (Part 1, steps 4–12). Branch is
named as `CLAUDE.md` says (`NN-short-slug`, no `NN-` without an issue).

It runs once per feature. The developer's workflow ends when this
command opens the PR. Approval and merging happen on GitHub, by the
reviewer. This command switches back to `main` at the end; the next
`/feature describe` pulls the latest `main` from GitHub first.

Only if the reviewer requests changes does the branch get revisited.
That is an exception, not part of this command: it follows Part 2
(steps 13–17) of `claude-context/project-git-workflow.md`. Don't run
`/feature finalize` again for it.

## Before running anything

Check the current branch. If it is `main`, stop: there is no feature
branch to finalize. The spec is `claude-context/<current-branch>-spec.md`. If
it doesn't exist, stop and ask. If its **Status** is `PR created` or
`Documented`, or
`gh pr view` shows a PR already exists for this branch, stop and point
the user to Part 2 of `claude-context/project-git-workflow.md`. If its
**Status** is `New` or `Implemented`, stop: the feature hasn't been
tested yet. Point the user to the next step (`/feature implement` or
`/feature test`).

Read `CLAUDE.md`, `claude-context/ai-interaction.md` and
`claude-context/project-git-workflow.md` if not already fresh in context.
Read the spec's **Mode** line and state it in one line (e.g. "Mode:
SURF"). No **Mode** line means `VERBOSE`. See *Modes* in `SKILL.md`.

Every git and `gh` command below is shown before it runs, one action at
a time. In VERBOSE, wait for a go-ahead on each. In SURF, run them
without waiting, except `git push` and `gh pr create`, which wait in both
modes. Never push to `main`, never
force-push. No commit message and no PR title or body may credit Claude
or any AI tool: no `Co-Authored-By` line, no "Generated with Claude Code"
line.

Steps marked *(migrations only)* apply only if `CLAUDE.md` has a
*Database migrations* section. Skip them otherwise.

## Steps, in this order

1. **Commit everything outstanding.** Run `git status` on the feature
   branch. Read the file list before staging: nothing from *Never
   commit* in `CLAUDE.md`, no other junk (a hit always stops, in both
   modes). Then `git add -A` and the exact
   commit command. Don't continue until `git status` says the tree is
   clean.
2. **Sync with `main`.** `git fetch`, then `git merge origin/main`. If
   there are conflicts, stop and explain them file by file; the user
   decides, or runs `git merge --abort`. This stop applies in both modes.

   *(migrations only)* After the merge, run the single-head check from
   `CLAUDE.md`. Two latest migrations means both branches added one. To
   fix it, explain the problem and wait for a go-ahead (both modes),
   then:
   - If the dev DB already ran this branch's migration, roll back to the
     migration before it first, by naming that migration (the
     roll-back-to command from `CLAUDE.md`), so the DB doesn't hold a
     migration that's about to move. "Roll back one" doesn't work here:
     with two latest migrations, the tool can't tell which one is meant.
   - Make this branch's migration follow the latest one from `main`
     (e.g. its parent/`down_revision`), and commit.

   If the merge brought in new commits from `main` (it didn't say
   "Already up to date"), stop here. The tests from `/feature test` ran
   without the team's latest work, so tell the user to run
   `/feature test` again, then `/feature finalize` again. Don't run the
   tests yourself.
3. **Run the checks.** Start any local services listed under *Commands*
   in `CLAUDE.md`. *(migrations only)* Apply all migrations; if the
   branch adds a migration, roll back one and apply again. Then run every
   command under *Checks* in `CLAUDE.md`, in order, except the test
   commands: tests are `/feature test`'s job.

   If anything fails: in VERBOSE, explain the failure in plain language,
   propose the fix, and wait for a go-ahead. In SURF, fix it, then
   explain in plain language what was wrong and what you changed. After
   each fix:
   - commit it (step 1's rules)
   - *(migrations only)* if the fix adds or changes a migration, rerun
     the single-head check from step 2
   - rerun every check above, not only the one that failed

   Keep iterating until everything passes, however many rounds it
   takes. Don't continue to step 4 until all checks pass.

   *Good to know:* most fixes here only change how the code looks
   (formatting, style), which doesn't affect the tests. If a fix changes
   what the code does — e.g. removing a line the linter calls unused —
   stop after committing it, and tell the user to run `/feature test`
   again, then `/feature finalize` again, so the tests cover the
   changed code.
4. **Close the spec.** Change its **Status** line to `PR created`, so it
   reads as a finished feature, not a new one, and commit it. Don't edit
   the design docs or `claude-context/Project Overview/`. While still on the
   branch, compare `git diff origin/main...HEAD` with
   `docs/architecture.md` and `docs/project-phase-plan.md`, and note any
   major change for step 8.
5. **Push.** Show `git push -u origin <branch>` and wait for a
   go-ahead, in both modes.
6. **Open the PR.** Build the title and body, show them in full, and wait
   for a go-ahead before running `gh pr create`, in both modes.
   - **With an issue** (the spec's **Issue** line): get its title with
     `gh issue view N`. The PR title is `NN — <issue title>`, where `NN`
     is the number prefix of the branch name (e.g. `07`). The body
     starts with `Closes #N`.
   - **Without an issue** (**Issue:** none): the PR title is the
     *Feature name* line at the top of the spec, and the body has no
     `Closes` line.
   - Body sections: *What this adds*, *Verified locally*, *Not in this
     PR*. *Verified locally* lists the step 3 check results, the test
     results from the spec's **Test results** line (from
     `/feature test`), and the spec's **How to see it working:** check
     the user ran by hand.
7. **Return to `main`.** Show `git checkout main` (in VERBOSE, wait for
   a go-ahead), so the next `/feature describe` starts from `main`.
8. **Stop.** Give the user the PR link and say it's waiting for a
   teammate to approve and merge it. If step 4 noted a major change to
   what `docs/architecture.md` or `docs/project-phase-plan.md` describe (data model, architecture, tech stack,
   phase scope), add a short, to-the-point summary of it. Otherwise
   leave it out. Once the PR is merged, `/professorslughorn` brings the
   feature's changes into the documentation. Only if the reviewer requests
   changes: those go on this same branch, by hand, following Part 2 of
   `claude-context/project-git-workflow.md`. Don't merge, and don't delete the
   branch.
