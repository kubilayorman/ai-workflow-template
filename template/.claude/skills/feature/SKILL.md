---
name: feature
description: Slash command only, with four sub-actions — /feature describe [VERBOSE|SURF] "<prompt>", /feature implement @<spec-file>, /feature test, /feature finalize — used to plan, build, test, and ship a feature or GitHub issue in this project. Do not trigger from plain conversation without one of these exact sub-actions.
---

# feature

Router for the feature workflow. Read only the action file matching what was typed, plus the shared sections below, and follow it exactly.

| Command | Action file | What it does |
|---|---|---|
| `/feature describe [VERBOSE\|SURF] "<prompt>"` | [describe.md](describe.md) | Turns the prompt into a spec under `claude-context/`, with the feature's mode. |
| `/feature implement @<spec-file>` | [implement.md](implement.md) | Builds the named spec. No tests. |
| `/feature test` | [test.md](test.md) | Writes tests after the user approves a test plan, and runs the suite. |
| `/feature finalize` | [finalize.md](finalize.md) | Runs the non-test checks, pushes, and opens a PR. No tests. |

## Routing rule

Look at the first word after `/feature`:

- `describe` → follow `describe.md`. If the next word is `VERBOSE` or `SURF` (any case), that's the mode; the rest is the prompt. Otherwise the mode is `VERBOSE` and everything after `describe` is the prompt.
- `implement` → follow `implement.md`. Everything after `implement` is the spec file.
- `test` → follow `test.md`.
- `finalize` → follow `finalize.md`.
- Anything else, or nothing → ask which of the four they meant. Don't guess.

Each action starts only from its own explicit `/feature ...` command. Never skip ahead because the conversation sounds ready.

## Lifecycle

The actions run once each per feature, in order: describe → implement → test → finalize. (`test`, then `finalize`, run again if `finalize` sends the user back.) Only `/feature test` writes or runs automated tests.

The spec's **Status** line tracks progress:

| Status | Set by |
|---|---|
| `New` | `/feature describe` |
| `Implemented` | `/feature implement` |
| `Tested` | `/feature test` |
| `PR created` | `/feature finalize` |
| `Documented` | `/professorslughorn`, after the PR is merged and the docs cover it |

The developer's workflow ends when `/feature finalize` opens the PR; the reviewer merges on GitHub. Requested changes aren't a `/feature` action: they follow Part 2 of `claude-context/project-git-workflow.md`, by hand. After merging, `/professorslughorn` updates the docs.

## Modes

The mode is chosen at `/feature describe` and stored in the spec's **Mode** line; the user can edit it mid-feature.

- **VERBOSE** (default): show every git and `gh` command and wait for a go-ahead before running it, one at a time. When a test or check fails, explain it, propose the fix, and wait before fixing.
- **SURF**: run git commands (`pull`, `checkout`, `add`, `commit`, `fetch`, `merge`, `checkout main`) without waiting, still showing each. When a test or check fails, fix it, rerun, then explain in plain language what was wrong and what changed.

Any other "wait for a go-ahead" is VERBOSE only. These **stops apply in both modes**:

- the mode question and clarifying questions in `/feature describe`
- the test plan table in `/feature test`
- `git push` and `gh pr create`, with the PR title and body shown in full
- merge conflicts, and the migration single-head fix in `/feature finalize`
- anything from *Never commit* in `CLAUDE.md` showing up in `git status`
- big structural decisions or new dependencies (`ai-interaction.md`)
- every "stop" in an action's own guard (wrong branch, wrong **Status**, spec not found, and so on)

The mode never chains actions.

## Every action

`implement`, `test` and `finalize` all do these.

- **Read the rules:** `CLAUDE.md`, `claude-context/ai-interaction.md` and the spec (it may have been edited), if not fresh in context.
- **Spec from the branch** (`test`, `finalize`): if the current branch is `main`, stop — there's no feature branch. The spec is `claude-context/<current-branch>-spec.md`; if it doesn't exist, stop and ask.
- **State the mode:** read the spec's **Mode** line and state it in one line (e.g. "Mode: SURF"). No **Mode** line means `VERBOSE`.
- **Show commands:** every git and `gh` command is shown before it runs, one action at a time.
- **Commit routine:** run `git status` and check the file list against *Never commit* in `CLAUDE.md` (a hit always stops). Then show the exact `git add -A` and commit commands; VERBOSE waits, SURF runs them.
- **No AI credit:** no commit message, PR title or PR body may credit Claude or any AI tool — no `Co-Authored-By` line, no "Generated with Claude Code" line.
