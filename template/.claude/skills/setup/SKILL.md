---
name: setup
description: Slash command only — /setup — prepares a project that this template was just copied into. The project's technical files must already exist. It interviews the user, checks the answers against the code, sets up testing (tests/ folder, and the test tools the user approves), and writes docs/architecture.md, docs/project-phase-plan.md and CLAUDE.md (from claude-template.md). Do not trigger from plain conversation without /setup.
---

# setup

## What this does

Interviews the user, checks the answers against the code, then sets up `tests/` and writes the design docs and `CLAUDE.md`. It doesn't scaffold: the project must already have its technical files, and the template must be installed (see the template repo's README). It installs only the test tools the user approves, runs nothing else, and commits nothing.

## Steps

Do these in order. Write every message in plain language: no jargon, or explain it in a few words.

### 1. Check the template is there

If `claude-template.md` isn't in the project root, tell the user the template wasn't copied in fully, and stop.

### 2. Check the project has its technical files

List the project root, including hidden files. If it holds nothing but:

- template files: `.claude/`, `claude-context/`, `claude-template.md`
- harmless extras: `.git/`, `.DS_Store`, `README.md`, `.gitignore`, `LICENSE`, an empty `docs/`

then it isn't scaffolded yet. Tell the user to run the scaffold command for their project type from `scaffolding.md` in the template repo (it creates a new folder), install the template there, and run `/setup` there. Then stop.

### 3. Look at the project

Read what shows what the project is and how it runs, without changing anything:

- **First:** any existing `CLAUDE.md`, `docs/architecture.md` and `docs/project-phase-plan.md`.
- **Then, e.g.:** `README.md`, dependency files (`package.json`, `pyproject.toml`, `requirements.txt`, `Gemfile`, `go.mod`, …), CI settings (`.github/workflows/`), `docker-compose.yml`, the schema and migration folders, the main source folders, test folders and their shared helpers, `.gitignore`.
- **Test tools already set up:** `vitest`, `jest`, `@playwright/test` or `pytest` in the dependency files, and configs like `vitest.config.*`, `jest.config.*`, `playwright.config.*`, `pytest.ini` or `[tool.pytest.ini_options]` in `pyproject.toml`.
- **Images in `claude-context/ui-prototypes/`:** hints about screens, users and the core flow.

Note how much the project does beyond its starter files; that sets how much weight the code carries in the interview.

### 4. Interview the user

Learn the business logic, data model, architecture, phases and testing well enough to write the design docs and `tests/README.md`. Ask the four rounds one per message, waiting for answers, and use earlier answers to make later questions concrete. The user can "skip" any question; note it for *Open questions*.

**Use the code.** Offer a suggested answer wherever the code or existing docs show one, so the user can reply "yes".

- **Mostly starter files:** the answers are the main source for the design.
- **Real work already in it:** check each answer against the code. Where they differ, say what the code shows and ask which is right. An intended-but-unbuilt answer is used, with the difference under *Open questions*. Never settle a difference silently.

**Round 1: The app**

1. What's the project called, what will the app do, and what problem does it solve?
2. Who will use it? The types of users (e.g. customer, staff, admin), what each can do, and whether they sign in.
3. Walk through the most important thing a user does, step by step.

**Round 2: Data and rules**

4. What does the app keep track of, and how is it connected? (e.g. a customer has reservations, each for one table) Mention key details, such as a reservation's date, time and party size.
5. What rules does it follow — limits, statuses, prices, deadlines, who may do what? (e.g. a reservation can be cancelled up to 24 hours before)
6. Does it store anything sensitive, such as personal details, payments or health data?

**Round 3: Shape and scope**

7. Where will people use it: web browser, phone app, both, or only as an API or script? (If it has screens, mockups or screenshots can go in `claude-context/ui-prototypes/` at any time; UI features will follow them.)
8. Does it need outside services, such as payments, email or SMS, maps, AI, or Google sign-in?
9. What phase is the project in, and what must that phase include? What comes after, and what can wait? (With real work in it, suggest a first phase summing up what's built.)

**Round 4: Testing**

10. Suggest how to test the app, then ask: "Here's how I suggest testing <app>. Keep it, change a tool, or drop a layer?"

Base it on the code and rounds 1–3. Show one row per layer: **Layer**, **What it checks** (with an example from this app, e.g. "a booking for 9 people is refused"), **Tool**.

- **Unit** — always: business rules, calculations, validation.
- **Integration** — with an API, database or outside service: an endpoint or query working inside the app.
- **End-to-end (e2e)** — with screens: a real user flow clicked through, e.g. the round 1 core flow.

| Project | Unit | Integration | E2E |
|---|---|---|---|
| Next.js | Vitest + React Testing Library | Vitest (route handlers, server actions) | Playwright |
| React + Vite | Vitest + React Testing Library | Only if it has its own API: Vitest | Playwright |
| Node.js API (Express) | Vitest | Vitest + Supertest | — |
| Python API (FastAPI) | pytest | pytest + FastAPI's `TestClient` | Playwright, only if it serves pages |
| Python script or CLI | pytest | pytest, running the command | — |
| Mobile app (Expo) | Jest (`jest-expo`) + React Native Testing Library | — | Maestro (optional) |
| Other | The stack's usual test runner | Same runner | Playwright for web screens |

Also say:

- **With a database:** integration tests use a separate test database, or undo their changes after each test, and never touch real data.
- **With outside services** (payments, email, AI, …): tests never call them for real; they use stand-ins in `tests/helpers/`.

Test tools already in the project win; don't suggest switching. Unit tests can't be dropped; other layers can.

Ask few follow-ups, only where the data model or core flow is unclear.

### 5. Play back and confirm

Show a short summary and ask the user to confirm or correct it:

- the entities and how they connect, as a Mermaid `erDiagram`
- the core flow as numbered steps
- the tech stack, as found in the project's files
- testing: the layers, the tool for each, and the folders under `tests/`
- the phases: what the current one includes, and what comes later
- any differences between the answers and the code, and how each was settled

Write nothing until the user confirms. Play back again if corrections change the data model or phases.

### 6. Set up testing

Set up the testing confirmed in step 5.

1. **Create the folders.** Never overwrite or move existing test files.

   ```
   tests/
     README.md      how testing works in this project
     unit/          always
     integration/   only if that layer was chosen
     e2e/           only if that layer was chosen
     fixtures/      shared test data: sample users, payloads, records
     helpers/       shared setup and stand-ins for outside services
   ```

   - Put a `.gitkeep` in each empty folder.
   - For pytest, shared fixtures go in `tests/conftest.py` (pytest only finds them there); `helpers/` holds the rest.
   - If the project keeps tests elsewhere (e.g. `*.test.ts` next to the code), keep that, create only what's missing, and say so.

2. **Write `tests/README.md`** from `testing-template.md` next to this file. Fill in every section, remove unchosen layers and the template comments, and check no `{{` or `}}` is left. If one already exists, ask before replacing it.

3. **Show the install** and wait for a go-ahead — the only thing setup installs. Skip tools already installed.
   - The exact dev-dependency commands, e.g. `npm install -D vitest @testing-library/react jsdom @playwright/test` and `npx playwright install chromium`, or `uv add --dev pytest httpx`.
   - The config files and scripts you'll add, e.g. `vitest.config.ts` (include `tests/unit/**` and `tests/integration/**`), `playwright.config.ts` (`testDir: 'tests/e2e'`, `webServer` set to the app's run command so tests start it), `testpaths = ["tests"]` under `[tool.pytest.ini_options]`, and `test` / `test:e2e` scripts in `package.json`.

4. **Install and configure** after the go-ahead, with minimal configs. If the user says no, keep the folders and `README.md`, and note the missing tools for step 10.

### 7. Write the design docs

Create two files from the templates next to this one:

- `docs/architecture.md` from `architecture-template.md`.
- `docs/project-phase-plan.md` from `phase-plan-template.md` — a high-level blueprint: goals and scope per phase, no task lists.

Fill every section from the confirmed answers and the code; skipped or unclear items go under *Open questions*. Remove the template comments and check no `{{` or `}}` is left. If either file exists, ask before replacing it, and carry over anything still true.

### 8. Fill in CLAUDE.md

Fill each `{{placeholder}}` in `claude-template.md` that the files or confirmed answers answer clearly, with real commands and paths. Don't guess.

- The description sums up the *Overview* in `docs/architecture.md` in one or two sentences; the stack comes from the project's files.
- The test commands under *Checks* and the *Tests* section come from step 6 and `tests/README.md`.

If there's an old `CLAUDE.md`:

- Use its information to fill placeholders.
- Keep any rule or note without a place in the template, in the best-fitting section or under *Other guidance*.
- Where it disagrees with the project's files (e.g. a command that no longer exists), trust the files and note it for step 10.

Ask about every unfilled placeholder in **one** short numbered list, with suggested answers. Remove a line that doesn't apply or the user can't answer; note unanswered ones for step 10.

Remove a whole section only when it doesn't apply (e.g. *Database migrations* without a database) — never because an answer is missing, since other files refer to headings by name. If a kept section is empty, write "Not set yet." under it and note it for step 10.

Always keep *Source of truth*, naming `docs/architecture.md` and `docs/project-phase-plan.md`.

### 9. Write CLAUDE.md

Save `CLAUDE.md` in the project root, replacing any old one. Remove every template comment, including the "how to use this file" one at the top. Check that no `{{`, `}}` or `<!--` is left.

### 10. Report, then stop

Tell the user, in a short list:

1. `CLAUDE.md` is ready, and what it says the project is.
2. The design docs and the current phase. They're a snapshot; the code will move ahead of them.
3. Testing: layers, tools, folders (see `tests/README.md`), and what was installed — or the commands still to run.
4. Answer-vs-code differences left under *Open questions*.
5. With an old `CLAUDE.md`: what was carried over, and what was dropped as out of date.
6. Removed sections and unanswered questions, to add later.
7. `claude-template.md` isn't needed any more: they should delete it before committing. Don't delete it yourself.
8. If the app has screens: 1–4 images in `claude-context/ui-prototypes/` guide how features look. Say which exist.
9. What to do next:
   - **Commit everything first,** including `tests/` and the new configs: `/feature implement` stops if anything besides a spec is uncommitted.
     - Not on GitHub yet: commit on `main` (the exception in Team rule 1 of `claude-context/project-git-workflow.md`), create the GitHub repository, and push `main`.
     - Otherwise: commit on a new branch (e.g. `setup`) and open a PR, per Part 1 of `claude-context/project-git-workflow.md`. Once a teammate merges it, `git checkout main` and `git pull --ff-only` — until then `main` lacks the skills and `CLAUDE.md`.
   - **`/feature describe "<what you want>"`** starts a feature. Add `SURF` after `describe` for fewer approval stops; the default is `VERBOSE`.

<!--
Adding setup steps later: add each as a new numbered step before "Report, then stop", with a heading that says in plain words what it does. Update "What this does" if needed.
-->
