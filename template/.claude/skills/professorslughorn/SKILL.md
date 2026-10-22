---
name: professorslughorn
description: Slash command only — /professorslughorn — brings all project documentation up to date with the latest `main` after features are merged — the design docs (`docs/architecture.md`, `docs/project-phase-plan.md`), `CLAUDE.md` when something it describes has changed, and the onboarding docs in `claude-context/Project Overview/` (FILE_DESCRIPTIONS.md, DATABASE_OVERVIEW.md, PROJECT_OVERVIEW.md) — marks the merged feature specs as Documented, and opens a PR for the changes. Runs only from an up-to-date `main` with no open PRs. Do not trigger from plain conversation without /professorslughorn.
---

# professorslughorn

Brings all project documentation up to date with `main` after features are merged:

- the design docs `docs/architecture.md` and `docs/project-phase-plan.md`
- `CLAUDE.md`, only where something it describes has changed
- the three linked documents in `claude-context/Project Overview/`

No arguments; every run does Steps 0–8 in order.

The Project Overview rules live next to this file. If this file and a numbered file disagree, the numbered file wins.

- `00_GENERATION_RULES.md` — outputs, order, update mode, depth, cross-links, metadata. **Read every run.**
- `01_LANGUAGE_GUIDE.md` — writing rules. **Read every run.**
- `02_` / `03_` / `04_TEMPLATE_*.md` — one template per document.

The design docs are updated first, because the Project Overview reads `docs/architecture.md` for the core flow step numbers and 🛠️ Planned items.

## The opening line

Start the run's first message with exactly this line:

> This is all hypothetical, isn't it? All academic?

It's a joke, not a question. Don't wait for or ask about an answer; carry straight on with Step 0 in the same message. If the developer replies to it, ignore the reply.

## Step 0 — Check the starting point

The documents snapshot the latest `main`, so this never runs mid-feature. Show each git and `gh` command and wait for a go-ahead. Stop and say why if a check fails or a command is declined:

1. `git status`: on `main`, tree clean (no uncommitted or untracked files, including a spec from `/feature describe`).
2. `git pull --ff-only`: local `main` matches GitHub. If it fails, point to *Troubleshooting* in `claude-context/project-git-workflow.md`.
3. `gh pr list`: no open PRs, so the snapshot includes all work in progress.

## Step 1 — Find the merged features not documented yet

Specs are committed on feature branches, so a spec reaches `main` only when its PR is merged. After Step 0, every spec (`claude-context/*-spec.md`) with **Status** `PR created` is a merged, undocumented feature. Skip specs marked `Documented`.

For each `PR created` spec, look up its PR:

```
gh pr list --state merged --head <branch> --json number,title,mergedAt
```

`<branch>` is the file name without `-spec.md`. The PR number, title and merge date go into the report and PR body. If no PR is found, still document it and write "PR not found".

The specs explain *why* the code changed, but every document is checked against the code, so work merged without a spec is picked up too. Carry on even if no spec is waiting.

## Step 2 — Read the project

- **Start with the project's own words:** `README.md`, `CLAUDE.md` and the docs they point to. Trust the code first, then the design docs under *Source of truth* in `CLAUDE.md`, over issues and comments.
- **Read the specs from Step 1** and the code they touched.
- **Traverse the whole tree fresh,** following imports out from the entry points. Exclude `.git`, dependency folders, build output, lockfiles, `claude-context/Project Overview/`, and the template's folders `.claude/`, `claude-context/` and `docs/` (not app code, so no file descriptions).
- **Take "business entities"** for 00's depth rule from the project's domain model, not the templates' examples.

## Step 3 — Update the design docs and `CLAUDE.md`

Compare each file against the code and the Step 1 specs. Change only what no longer matches; keep every other section word for word.

- **`docs/architecture.md`:** domain model, fields, statuses, business rules, core flow, architecture, external services, tech stack.
  - Keep planned-but-unbuilt items: the doc also describes what the project will become.
  - Move *Open questions* the code or a spec has answered out of that list.
  - Update **Status** (Planned / Partly built / Built) and **Last updated** only if something changed.
  - Never rename or remove a section heading: the Project Overview maps them onto its documents.
- **`docs/project-phase-plan.md`:** a blueprint, not a progress tracker.
  - Change it only when a phase's scope changed (e.g. a feature planned for a later phase was built), or the merged features meet the current phase's **Done when** — then propose moving **Current phase** on.
  - Never add task lists or done markers. Update **Last updated** only if something changed.
- **`CLAUDE.md`:** only *Commands*, local services, *Checks*, *Tests*, *Database migrations* and *Never commit*, where the code shows they're out of date (e.g. a new command or a folder that must never be committed). Never rename or remove a heading: other files refer to them.

Then **stop and show one table** of every proposed change, and wait for a go-ahead before writing — these files hold the developer's intentions:

| File | Section | What changes | Why |
|---|---|---|---|
| `docs/architecture.md` | Domain model | Adds the `PasswordReset` entity | Spec `12-password-reset`, `app/models/reset.py` |

Apply their corrections first. If nothing needs changing, say so in one line and continue without stopping.

## Step 4 — Write or update each Project Overview document

In 00's order, create each document from its template or follow 00's *Update Mode*. Strip all template HTML comments. Run 00's *Final Check* and 01's *Final Language Check* before saving.

Items `docs/architecture.md` describes but the code lacks are included as 🛠️ Planned (00's *Projects Without Code Yet*). `docs/project-phase-plan.md` feeds only the *Phases* section of `PROJECT_OVERVIEW.md`.

Two rules the templates leave implicit:

- **What → how → why.** Every non-obvious choice gets a real reason. Where the project picked the unusual option, name the usual alternative and what goes wrong with it.
- **Explain, don't enumerate.** Architecture, flow and workflow sections are paragraphs. Bullets are for enumerable things: fields, commands, files.

## Step 5 — Link pass

`FILE_DESCRIPTIONS.md` is written before the tables and flow steps it links to. Once all three exist, add missing links to `DATABASE_OVERVIEW.md#table-*` and `PROJECT_OVERVIEW.md#flow-step-*`, and check every linked anchor exists.

## Step 6 — Mark the features as documented

In each Step 1 spec, set **Status** to `Documented` and add this line under the header lines, with today's date:

```markdown
**Documented:** YYYY-MM-DD
```

Change nothing else in a spec.

## Step 7 — Open a PR

Skip this if no file changed. A new date alone isn't a change (00's *Update Mode*); a spec marked `Documented` is.

Otherwise, show each command and wait for a go-ahead (the steps follow Part 1 of `claude-context/project-git-workflow.md`):

1. `git checkout -b docs-update-YYYY-MM-DD` (today's date).
2. `git add` only this run's files — `"claude-context/Project Overview/"`, `docs/`, `CLAUDE.md`, the Step 6 specs — and commit, e.g. `docs: update documentation`.
3. Run the checks in `CLAUDE.md`, then `git push -u origin <branch>`.
4. `gh pr create`, titled `Documentation update YYYY-MM-DD`. The body lists the sections changed per file, then *Features covered*, one line per spec (`#N — <PR title>, merged YYYY-MM-DD`). No `Closes` line.
5. `git checkout main`.

No commit message, PR title or body may credit Claude or any AI tool.

## Step 8 — Report, then stop

- Per file: created, updated (with the sections changed), or unchanged.
- The features documented, one line each.
- The PR link, and one line: a teammate approves and merges it on GitHub. If nothing changed, say no PR was needed.

No summary, offers or follow-up questions.

Other files in `claude-context/Project Overview/` (e.g. `project-overview-YYYY-MM-DD.md`, `db-overview.md`) aren't managed by this skill: never edit or delete them.
