# /feature finalize

## When this runs

Invoked as `/feature finalize`, after `/feature test` has passed. It doesn't write or run tests. It gets the feature branch ready and opens a PR for a teammate to approve, following Part 1 (steps 4–12) of `claude-context/project-git-workflow.md`.

It runs once per feature, plus again after sending the user back to `/feature test`. Requested changes don't rerun it (see *Lifecycle* in `SKILL.md`).

## Before running anything

Do *Every action* in `SKILL.md`: spec from the branch, read the rules, state the mode. Then stop if:

- **Status** is `PR created` or `Documented`, or `gh pr view` shows a PR for this branch → point to Part 2 of `claude-context/project-git-workflow.md`.
- **Status** is `New` or `Implemented` → not tested yet. Point to the next step (`/feature implement` or `/feature test`).

Also read `claude-context/project-git-workflow.md` (Part 1, *Team rules*, *Troubleshooting*). Never push to `main`, never force-push.

Steps marked *(migrations only)* apply only if `CLAUDE.md` has a *Database migrations* section.

## Steps, in this order

1. **Commit everything outstanding** with the *Commit routine* (no junk either), until `git status` says the tree is clean.

2. **Sync with `main`.** `git fetch`, then `git merge origin/main`. On conflicts, stop (both modes) and explain them file by file; the user decides, or runs `git merge --abort`.

   *(migrations only)* After the merge, run the single-head check from `CLAUDE.md`. Two latest migrations means both branches added one. Explain it and wait for a go-ahead (both modes), then:

   - If the dev DB already ran this branch's migration, first roll back to the migration before it **by name** (the roll-back-to command in `CLAUDE.md`). "Roll back one" can't work: with two latest migrations, the tool can't tell which is meant.
   - Make this branch's migration follow `main`'s latest (e.g. its parent/`down_revision`), and commit.

   **If the merge brought in new commits** (it didn't say "Already up to date"), stop here. The tests ran without the team's latest work, so tell the user to run `/feature test`, then `/feature finalize` again. Don't run the tests yourself.

3. **Run the checks.**
   - Start any local services under *Commands* in `CLAUDE.md`.
   - *(migrations only)* Apply all migrations. If the branch adds one, roll back one and apply again.
   - Run every command under *Checks* in `CLAUDE.md`, in order, except the tests (those are `/feature test`'s job).

   If anything fails, explain and fix it per *Modes*. After each fix:

   - commit it (*Commit routine*)
   - *(migrations only)* if the fix adds or changes a migration, rerun the single-head check from step 2
   - rerun every check, not only the one that failed

   Repeat until all checks pass.

   If a fix changes what the code does, not just how it looks (e.g. removing a line the linter calls unused), stop after committing it: the user runs `/feature test`, then `/feature finalize` again.

4. **Close the spec.** Set **Status** to `PR created`, so it reads as finished, and commit. Don't edit the design docs or `claude-context/Project Overview/`.

   Still on the branch, compare `git diff origin/main...HEAD` with `docs/architecture.md` and `docs/project-phase-plan.md`, and note any major change for step 8.

5. **Push** (stop, both modes). Show `git push -u origin <branch>` and wait for a go-ahead.

6. **Open the PR** (stop, both modes). Show the title and body in full and wait for a go-ahead before `gh pr create`.
   - **With an issue** (the spec's **Issue** line): get its title with `gh issue view N`. Title: `NN — <issue title>`, where `NN` is the branch's number prefix (e.g. `07`). The body starts with `Closes #N`.
   - **Without an issue:** the title is the spec's **Feature name**, with no `Closes` line.
   - **Body sections:** *What this adds*, *Verified locally*, *Not in this PR*. *Verified locally* lists the step 3 results, the spec's **Test results**, and the **How to see it working:** check the user ran by hand.

7. **Return to `main`.** Show `git checkout main`, so the next `/feature describe` starts there.

8. **Stop.** Give the user the PR link and say it's waiting for a teammate to approve and merge it.
   - If step 4 noted a major change to what the design docs describe (data model, architecture, tech stack, phase scope), add a short summary. Otherwise leave it out.
   - Once merged, `/professorslughorn` brings the changes into the docs.
   - Requested changes go on this same branch, by hand, per Part 2 of `claude-context/project-git-workflow.md`.
   - Don't merge, and don't delete the branch.
