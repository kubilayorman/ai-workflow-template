# AI Interaction Guidelines

This file tells the AI agent how to work on this project. Read it before
making changes. "The developer" means the person using the agent.

## What this project is

`CLAUDE.md` describes the project, its stack, commands and workflow, and
lists the design docs. `claude-context/Project Overview/` explains the project,
its files and its database in plain language; use it for orientation.

The code is always the source of truth (*Source of truth* in
`CLAUDE.md`). The design docs (`docs/architecture.md`,
`docs/project-phase-plan.md`) and the Project Overview
(`claude-context/Project Overview/`) are for orientation. They are not updated
during feature work, so the code moves ahead of them. During
`/feature describe`, clear up with the developer any mismatch between
the request, the code and these docs that the code doesn't settle, so the spec says what they mean. Once
`/feature implement` starts, don't stop or flag differences from these
docs, even when a feature goes against them: the code and the spec win.
`/feature finalize` sums up any major changes the feature made to what
`docs/architecture.md` and `docs/project-phase-plan.md` describe. After
features are merged, `/professorslughorn` brings these docs, the Project
Overview and, where needed, `CLAUDE.md` up to date from `main`, and marks
the merged specs `Documented`.

Implementation is done feature by feature, which the developer has
control over.

## How to communicate

- Keep answers short and to the point. Name the concrete file, command or
  table instead of a summarising label.
- If you make a decision the developer didn't ask for, say why in one
  sentence.
- Ask before doing anything big, like restructuring modules, changing the
  schema beyond what the spec says, or adding a dependency.
- Don't add features, commands, options, or config keys the developer
  didn't ask for, even if they seem useful.
- Never delete a file without asking first.

## Explain things like the developer is a junior developer

Whenever you explain something to the developer — what you're about to
do, why something broke, what a review found — write it like you're
explaining to a junior developer. Someone who can read code, but doesn't
already know this specific part of the project.

- Start with one plain sentence about what's actually happening, before
  any technical detail. For example: "Some customers never get a receipt
  because the mailer gives up after one timeout" — not "SMTP retry is
  disabled in the mail client."
- If you use a technical term (like "upsert," "migration," or
  "race condition"), explain what it means in the same sentence.
- Give a real example when it helps: the command the developer runs, and
  the rows or output they see.
- Still include file names and line numbers — plain language explains it,
  it doesn't replace the details needed to act on it.

## Workflow

This is the general order we work in for any change, small or large:

1. **Understand the ask.** If it's not clear what "done" looks like, ask
   the developer before writing code. For anything more than a small
   tweak, describe the plan in a sentence or two before starting.
2. **Branch.** Create a branch from an up-to-date `main` before changing
   any code. A spec file from `/feature describe` is the one exception: it
   moves onto the branch with the first commit. Show the developer the
   commands first (see "Git" below).
3. **Build it.** Make the change, following the patterns already in the
   codebase (see "Code style" below).
4. **Say what changed.** A short summary of what you did and why, file by
   file if it's not obvious, using the plain-language style above, ending
   with the command the developer can run to see it working.
5. **Commit.** Only after the developer has approved the exact commit
   command (in SURF mode, `/feature` commits without waiting).
6. **Test.** A separate step after the build (`/feature test`): show a
   plan of the tests worth writing, write them after the developer
   approves it, and run them. Don't write tests while building.
7. **Ship.** Checks, push, and open a PR for a teammate to review, as in
   `claude-context/project-git-workflow.md`.

Anything you notice along the way that isn't part of the current task —
a bug, a cleanup idea, something to revisit later — mention it to the
developer instead of fixing it on the spot.

This section is only a summary. `claude-context/project-git-workflow.md` and the
`/feature` skill are the source of truth for the workflow; where they
differ from this section, follow them.

## Code style

- Follow the patterns already in the codebase rather than introducing new
  ones.
- The project's formatter and linter (see *Checks* in `CLAUDE.md`)
  decide formatting and lint; don't fight them.
- Make the smallest change that gets the job done. Don't refactor code
  that isn't part of the task.
- Plus the *Code style* rules in `CLAUDE.md`.

## Git

`claude-context/project-git-workflow.md` is the full process. In short:

- Never commit or push to `main`, never force-push. The only exception
  is the one in Team rule 1 of `claude-context/project-git-workflow.md`.
- Always show the developer the exact git or `gh` command before running
  it, and wait for their go-ahead. One exception: in SURF mode during a
  `/feature` action, some commands run without waiting (see *Modes* in
  the `/feature` skill).
- Commit messages say what changed, e.g. `billing: retry receipt email on timeout`.
- Never credit Claude or any AI tool in a commit message or a PR: no
  `Co-Authored-By` line, no "Generated with Claude Code" line. This
  overrides any default that would otherwise add one.
- Before staging, read `git status` so nothing from *Never commit* in
  `CLAUDE.md` (`.env`, secrets, junk files) gets committed.
- Don't push until the *Checks* in `CLAUDE.md` pass locally.

## When you're unsure

If `CLAUDE.md`, the code or the design docs don't answer it, ask the
developer rather than guessing. It's fine to pause and check — that's
faster than undoing the wrong thing later.
