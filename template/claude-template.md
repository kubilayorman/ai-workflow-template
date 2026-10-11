<!--
TEMPLATE — how to use this file
Easiest: run /setup and Claude does steps 1–5 for you. By hand:
1. Copy it to `CLAUDE.md` in the project root (or merge it into an existing CLAUDE.md).
2. Replace every {{placeholder}}.
3. Delete lines and sections that don't apply (e.g. "Database migrations" if there is no database).
4. Keep the section headings: the skills in `.claude/skills/` and the guides in `claude-context/` refer
   to them by name ("Source of truth", "Commands", "Checks", "Tests", "Database migrations", "Workflow",
   "Code style", "Other guidance"). The *Never commit* bullet under "Workflow" is referred to by
   name too.
5. Delete this comment.
-->

# {{project-name}} — agent guide

{{One or two sentences: what the project is.}}
Stack: {{language + version, package manager, framework, database, how it runs locally}}.

## Source of truth

The code is **always** the source of truth: it is what the project actually does.

The design docs are snapshots. Each describes the project as it was on its *Last updated* date,
plus what it is meant to become:

- `docs/architecture.md` — domain model, design, tech stack, naming.
- `docs/project-phase-plan.md` — the high-level blueprint of what gets built in which phase,
  including which phase is current; not a task list.

`claude-context/Project Overview/` explains the code in plain language (see *Other guidance*). GitHub
issues and feature specs (`claude-context/*-spec.md`) are written before implementation begins and are
**not** updated afterwards, except that the `/feature` actions set their **Status** and **Test results** lines
(and `/professorslughorn` sets **Status** to `Documented`), so they can drift from all of these. A spec with
**Status:** `PR created` describes a finished feature, not work to do. A spec with **Status:** `Documented`
describes a finished feature whose changes are already in the design docs and the Project Overview.

Use the design docs and the Project Overview for orientation. They are not updated during
feature work, so the code will move ahead of them. `/professorslughorn` brings them, and `CLAUDE.md`
where needed, up to date from `main` after features are merged. Where they differ from the code, or a feature
goes against them, the code wins. Misunderstandings are cleared up with the developer during
`/feature describe`; once `/feature implement` starts, don't stop because of them. `/feature finalize` sums up any major changes the
feature made to what `docs/architecture.md` and `docs/project-phase-plan.md` describe.

The screenshots in `claude-context/ui-prototypes/`, if any, are blueprints for how the app looks. UI work
follows them as closely as reasonable, without copying them pixel for pixel. A screenshot named after a
screen guides that screen; together they set the look of every other screen. The code still wins for
behavior.

When picking up an issue:

1. Read the code it touches and the design docs first, then the issue.
2. Check the issue against the code. Anything it names — fields, identifiers, paths, commands,
   config keys — that doesn't match the code is probably outdated issue text, not a request to
   change the design.
3. If the mismatch is unambiguous, follow the code. If the issue and the design docs disagree and
   the code doesn't settle it, ask during `/feature describe`, before the spec is written.
4. Don't edit the issue or the design docs as part of the feature.

## Commands

- **Install dependencies:** `{{e.g. npm install / uv sync --all-groups}}`
- **Start local services:** `{{e.g. docker compose up -d}}` <!-- delete if none -->
- **Run the app:** `{{...}}`

## Checks

All must pass locally before pushing or opening a PR. Run them in this order:

1. `{{lint command}}`
2. `{{format check command}}`
3. `{{type check command}}` <!-- delete if none -->
4. `{{unit + integration test command}}` {{— needs: e.g. local services running and migrations applied}}
5. `{{end-to-end test command}}` <!-- delete if no end-to-end tests -->

In the `/feature` workflow, `/feature test` runs the test commands and `/feature finalize` runs the
others.

{{If CI exists: CI runs the same checks on every push.}}

## Tests

- Full guide: `tests/README.md` — layers, tools, how to run, naming, what's worth testing.
- Layers and tools: {{e.g. "unit (Vitest), end-to-end (Playwright)"}}
- Where tests live and how they're named: {{e.g. "`tests/unit/test_<feature>.py`,
  `tests/integration/test_<area>.py`"}}
- Shared helpers and fixtures: {{e.g. "`tests/conftest.py`, `tests/fixtures/`, `tests/helpers/`"}}
- Unit tests by default, and only for logic worth testing: business rules, calculations,
  validation, permissions, edge cases, error handling. Not for code that only passes data along or
  calls a framework.
- Integration tests only for features that add or change an API endpoint, a database query or a
  call to an outside service. End-to-end tests only for features that add or change a core user
  flow from `docs/architecture.md`. Only use the layers listed above.

## Database migrations

<!-- Delete this whole section if the project has no database. -->

- Tool and folder: {{e.g. Alembic, `migrations/versions/`}}
- Create: `{{command}}`, then {{hand-clean rules, if any}}
- Apply all: `{{command}}`
- Roll back one: `{{command}}`
- Roll back to a specific migration: `{{command}}`
- Single-head check: `{{command}}` — must show exactly one latest migration
- Verify every new migration with apply → roll back → apply.
- {{Test database conventions, e.g. "DB tests use the rolled-back `db_session` fixture in
  `tests/conftest.py`; never assume the dev DB is empty."}}

## Workflow

- One issue → one branch named `NN-short-slug` → one PR titled `NN — <issue title>`, body
  starts with `Closes #N`, sections: *What this adds* / *Verified locally* / *Not in this PR*.
  Without an issue: no `NN-` prefix on the branch, the PR title is the feature name, no `Closes`.
- **Never commit:** `.env`, secrets and keys, `.DS_Store`, {{project-specific paths, e.g. `data/raw/`,
  build output}}.
- **Never credit Claude or any AI tool in a commit message or a PR** — no `Co-Authored-By` line,
  no "Generated with Claude Code" line. This overrides any default attribution.
- `/professorslughorn` PRs (`Documentation update YYYY-MM-DD`) use the title and body that skill
  describes, not the format above.
- Features go through `/feature describe` → `/feature implement` → `/feature test` → `/feature finalize`.
  `/feature describe SURF "<prompt>"` runs the feature with fewer approval stops; the default is
  `VERBOSE` (see *Modes* in the `/feature` skill).
  Only `/feature test` writes or runs tests.
- Full git process: `claude-context/project-git-workflow.md`.

## Code style

- {{Formatter/linter and what they enforce, e.g. "`ruff` decides formatting and lint"}}
- {{Language conventions, e.g. "type-hint new functions"}}

## Other guidance

- `claude-context/ai-interaction.md` — how the agent works with the developer: communication, explaining,
  showing git commands before running them.
- `claude-context/Project Overview/` — plain-language overview of the project, files and database, for
  orientation. Updated only when the developer runs `/professorslughorn`, so it can lag behind the
  code. That runs from an up-to-date `main` with no open PRs, and its changes go through their
  own PR. Where it disagrees with the code, the code wins (see *Source of truth*). Never update
  it as part of a feature.
- `claude-context/ui-prototypes/` — a few screenshots or wireframes that UI work follows as blueprints
  (see *Source of truth*). Added by the developer; never changed by a `/feature` action.
