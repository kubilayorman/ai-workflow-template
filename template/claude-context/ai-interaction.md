# AI Interaction Guidelines

How the AI agent works on this project. Read it before making changes. "The developer" means the person using the agent.

## What this project is

**The code is always the source of truth** (*Source of truth* in `CLAUDE.md`). The design docs (`docs/architecture.md`, `docs/project-phase-plan.md`) and the Project Overview aren't updated during feature work, so the code moves ahead of them.

- **During `/feature describe`:** clear up with the developer any mismatch between the request, the code and these docs that the code doesn't settle.
- **From `/feature implement` on:** don't stop or flag differences from these docs, even when a feature goes against them. The code and the spec win.
- **After merges:** `/professorslughorn` brings the docs up to date from `main`.

## How to communicate

- Keep answers short. Name the concrete file, command or table, not a summarising label.
- If you make a decision the developer didn't ask for, say why in one sentence.
- Ask before anything big: restructuring modules, changing the schema beyond the spec, or adding a dependency.
- Don't add features, commands, options or config keys nobody asked for, even useful ones.
- Never delete a file without asking first.

## Explain things like the developer is a junior developer

Whenever you explain something — what you're about to do, why something broke, what a review found — write for a junior developer: someone who reads code but doesn't know this part of the project.

- **Start with one plain sentence** about what's happening, before any detail. E.g. "Some customers never get a receipt because the mailer gives up after one timeout" — not "SMTP retry is disabled in the mail client."
- **Explain technical terms** ("upsert", "migration", "race condition") in the same sentence.
- **Give a real example** when it helps: the command they run, and the output they see.
- **Keep file names and line numbers.** Plain language explains; it doesn't replace the details needed to act.

## Workflow

For any change, small or large:

1. **Understand the ask.** If "done" isn't clear, ask before writing code. For more than a small tweak, describe the plan in a sentence or two first.
2. **Branch** from an up-to-date `main` before changing code. (A `/feature describe` spec moves onto the branch with the first commit.)
3. **Build** following the codebase's patterns, then **say what changed**, file by file if not obvious, ending with the command to see it working.
4. **Commit** only after the developer approves the exact command (SURF mode excepted, see *Modes* in the `/feature` skill).

Tests, checks, push and PR follow the `/feature` skill and `claude-context/project-git-workflow.md`, which win over this summary.

Mention anything off-task you notice — a bug, a cleanup idea — instead of fixing it on the spot.

## Code style

- Follow the patterns already in the codebase rather than introducing new ones.
- The formatter and linter (*Checks* in `CLAUDE.md`) decide formatting; don't fight them.
- Make the smallest change that does the job. Don't refactor code outside the task.
- Plus the *Code style* rules in `CLAUDE.md`.

## Git

The full process is in `claude-context/project-git-workflow.md`. In short:

- **Never commit or push to `main`, never force-push.** The only exception is Team rule 1 there.
- **Show the exact git or `gh` command before running it,** and wait for a go-ahead (except where SURF mode says otherwise).
- **Commit messages say what changed,** e.g. `billing: retry receipt email on timeout`.
- **Never credit Claude or any AI tool** in a commit or PR: no `Co-Authored-By` line, no "Generated with Claude Code" line. This overrides any default that would add one.
- **Read `git status` before staging,** so nothing from *Never commit* in `CLAUDE.md` (`.env`, secrets, junk) gets in.
- **Don't push until the *Checks* in `CLAUDE.md` pass** locally.

## When you're unsure

If `CLAUDE.md`, the code or the design docs don't answer it, ask rather than guess. Pausing to check is faster than undoing the wrong thing.
