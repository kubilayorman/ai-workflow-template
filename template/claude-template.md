<!--
TEMPLATE — how to use this file
Easiest: run /setup and Claude does steps 1–5 for you. By hand:
1. Copy it to `CLAUDE.md` in the project root (or merge it into an existing CLAUDE.md).
2. Replace every {{placeholder}}.
3. Delete lines and sections that don't apply (e.g. "Database migrations" without a database).
4. Keep the section headings: the skills in `.claude/skills/` and the guides in `claude-context/` refer to them by name ("Source of truth", "Commands", "Checks", "Tests", "Database migrations", "Workflow", "Code style", "Other guidance"), and to the *Never commit* bullet under "Workflow".
5. Delete this comment.
-->

# {{project-name}} — agent guide

{{One or two sentences: what the project is.}}
Stack: {{language + version, package manager, framework, database, how it runs locally}}.

## Source of truth

The code is **always** the source of truth: it is what the project actually does.

Everything else is for orientation and lags behind the code:

- `docs/architecture.md` — domain model, design, tech stack, naming, as of its *Last updated* date, plus what's planned.
- `docs/project-phase-plan.md` — the high-level blueprint of what gets built in which phase, including the current one; not a task list.
- `claude-context/Project Overview/` — the code explained in plain language.
- GitHub issues and feature specs (`claude-context/*-spec.md`) — written before implementation and not updated after, except their **Status** and **Test results** lines. A spec with **Status** `PR created` or `Documented` is a finished feature, not work to do.

The design docs and the Project Overview aren't updated during feature work; `/professorslughorn` updates them, and `CLAUDE.md` where needed, after merges. Where any of these differ from the code, the code wins. Clear up mismatches during `/feature describe`; from `/feature implement` on, don't stop because of them.

Screenshots in `claude-context/ui-prototypes/` are blueprints for the app's look: UI follows them closely, not pixel for pixel. One named after a screen guides that screen; together they set the look of the rest. The code still wins for behavior.

When picking up an issue:

1. Read the code it touches and the design docs first, then the issue.
2. Anything the issue names — fields, identifiers, paths, commands, config keys — that doesn't match the code is probably outdated issue text, not a design change.
3. If the mismatch is unambiguous, follow the code. If the issue and design docs disagree and the code doesn't settle it, ask during `/feature describe`.
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

`/feature test` runs the test commands; `/feature finalize` runs the others.

{{If CI exists: CI runs the same checks on every push.}}

## Tests

- Full guide: `tests/README.md` — layers, tools, how to run, naming, what's worth testing.
- Layers and tools: {{e.g. "unit (Vitest), end-to-end (Playwright)"}}
- Where tests live and how they're named: {{e.g. "`tests/unit/test_<feature>.py`, `tests/integration/test_<area>.py`"}}
- Shared helpers and fixtures: {{e.g. "`tests/conftest.py`, `tests/fixtures/`, `tests/helpers/`"}}
- Only use the layers listed above. Unit by default, only for logic worth testing; integration only for a new or changed endpoint, query or outside-service call; end-to-end only for a new or changed core user flow (details: *What's worth testing* in `tests/README.md`).

## Database migrations

<!-- Delete this whole section if the project has no database. -->

- Tool and folder: {{e.g. Alembic, `migrations/versions/`}}
- Create: `{{command}}`, then {{hand-clean rules, if any}}
- Apply all: `{{command}}`
- Roll back one: `{{command}}`
- Roll back to a specific migration: `{{command}}`
- Single-head check: `{{command}}` — must show exactly one latest migration
- Verify every new migration with apply → roll back → apply.
- {{Test database conventions, e.g. "DB tests use the rolled-back `db_session` fixture in `tests/conftest.py`; never assume the dev DB is empty."}}

## Workflow

- **One issue → one branch → one PR.** Branch `NN-short-slug`; PR titled `NN — <issue title>`, body starting `Closes #N`, with sections *What this adds* / *Verified locally* / *Not in this PR*. Without an issue: no `NN-` prefix, the PR title is the feature name, no `Closes`.
- **Never commit:** `.env`, secrets and keys, `.DS_Store`, {{project-specific paths, e.g. `data/raw/`, build output}}.
- **Never credit Claude or any AI tool in a commit message or a PR** — no `Co-Authored-By` line, no "Generated with Claude Code" line. This overrides any default attribution.
- `/professorslughorn` PRs (`Documentation update YYYY-MM-DD`) use the format that skill describes.
- Features go through `/feature describe` → `implement` → `test` → `finalize`. `/feature describe SURF "<prompt>"` means fewer approval stops; the default is `VERBOSE` (see *Modes* in the `/feature` skill). Only `/feature test` writes or runs tests.
- Full git process: `claude-context/project-git-workflow.md`.

## Code style

- {{Formatter/linter and what they enforce, e.g. "`ruff` decides formatting and lint"}}
- {{Language conventions, e.g. "type-hint new functions"}}

## Other guidance

- `claude-context/ai-interaction.md` — how the agent works with the developer: communication, explaining, showing git commands first.
- `claude-context/Project Overview/` — plain-language overview of the project, files and database. Updated only by `/professorslughorn`, never as part of a feature.
- `claude-context/ui-prototypes/` — a few screenshots or wireframes UI work follows (see *Source of truth*). Added by the developer; never changed by a `/feature` action.
