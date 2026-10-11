---
name: feature
description: Slash command only, with four sub-actions — /feature describe [VERBOSE|SURF] "<prompt>", /feature implement @<spec-file>, /feature test, /feature finalize — used to plan, build, test, and ship a feature or GitHub issue in this project. Do not trigger from plain conversation without one of these exact sub-actions.
---

# feature

Router for the feature workflow. Each action is a separate,
self-contained instruction file in this same folder. Read only the one
that matches what was typed, and follow it exactly — don't read the
others unless that file says to.

| Command | Action file | What it does |
|---|---|---|
| `/feature describe [VERBOSE\|SURF] "<prompt>"` | [describe.md](describe.md) | Turns the prompt into a spec file under `claude-context/`, and sets the feature's mode. |
| `/feature implement @<spec-file>` | [implement.md](implement.md) | Builds the spec file named in the argument. No tests. |
| `/feature test` | [test.md](test.md) | Writes tests for the parts worth testing (unit by default; integration or end-to-end where the feature calls for them), after the user approves a test plan, and runs the test suite. |
| `/feature finalize` | [finalize.md](finalize.md) | Runs the non-test checks, marks the spec done, pushes the branch, and opens a PR for a teammate to approve. No tests. |

## Routing rule

Look at the first word typed after `/feature`:

- `describe` → read and follow `describe.md`. If the next word is
  `VERBOSE` or `SURF` (any case), that's the mode (see *Modes*);
  everything after it is the feature prompt. Otherwise the mode is
  `VERBOSE` and everything after `describe` is the prompt.
- `implement` → read and follow `implement.md`. Everything after the
  word `implement` is the spec file.
- `test` → read and follow `test.md`.
- `finalize` → read and follow `finalize.md`.
- Anything else, or nothing at all → ask the user which of the four
  they meant. Don't guess.

These four always run in this order across a feature's lifecycle:
describe → implement → test → finalize, once each per feature (`test`
runs again if `finalize` sends the user back to it).

Only `/feature test` writes or runs automated tests. The spec's
**Status** line tracks where a feature is: `New` (describe) →
`Implemented` (implement) → `Tested` (test) → `PR created` (finalize) →
`Documented`. `Documented` isn't set by a `/feature` action: `/professorslughorn`
sets it after the PR is merged, once the feature's changes are in the
documentation. Never skip ahead
to a later action just because the conversation sounds ready for it —
each one only starts from its own explicit `/feature ...` invocation.

The developer's workflow ends when `/feature finalize` opens the PR.
Approval and merging happen on GitHub, by the reviewer. If the reviewer
requests changes instead, that is not a `/feature` action: it follows
Part 2 of `claude-context/project-git-workflow.md`. After merging, the
developer runs `/professorslughorn` from `main` to bring the
documentation up to date.

## Modes

Each feature runs in one of two modes, chosen at `/feature describe` and
stored in the spec's **Mode** line. Every later action reads it from the
spec and states it in one line when it starts (e.g. "Mode: SURF"). The
user can switch mid-feature by editing that line.

- **VERBOSE** (default): show every git and `gh` command and wait for a
  go-ahead before running it, one action at a time. When a test or check
  fails, explain it, propose the fix, and wait before fixing.
- **SURF**: fewer stops. Run git commands (`pull`, `checkout`, `add`,
  `commit`, `fetch`, `merge`, `checkout main`) without waiting, still
  showing each one as it runs. When a test or check fails, fix it, rerun,
  then explain what was wrong and what you changed. Explanations stay in
  the same plain-language style as VERBOSE.

Stops kept in **both** modes, never skipped:

- clarifying questions in `/feature describe`
- the mode question at the start of `/feature describe`
- the test plan table in `/feature test`
- `git push` and `gh pr create`, with the PR title and body shown in full
- merge conflicts, and the migration single-head fix in `/feature finalize`
- anything from *Never commit* in `CLAUDE.md` showing up in `git status`
- big structural decisions or new dependencies (`ai-interaction.md`)
- every "stop" in an action's own checks (wrong branch, wrong **Status**,
  spec not found, and so on)

The mode never chains actions: each one still starts only from its own
`/feature ...` command. Where an action file says "wait for a go-ahead"
on a git command, a commit or a fix, that applies in VERBOSE only, unless
it's on the list above.
