---
name: setup
description: Slash command only — /setup — prepares a project that this template was just copied into. The project's technical files must already exist. It interviews the user, checks the answers against the code, sets up testing (tests/ folder, and the test tools the user approves), and writes docs/architecture.md, docs/project-phase-plan.md and CLAUDE.md (from claude-template.md). Do not trigger from plain conversation without /setup.
---

# setup

## What this does

Setup writes a well-written `CLAUDE.md` (the project's instruction file
for Claude) and the design docs it builds on. It doesn't create the
project's technical files: that is scaffolding, and you do it yourself
first.

**Before you run it:**

1. Scaffold the project, e.g. with a command from `scaffolding.md` in
   the template repo, or start from a project that already has code.
2. Install the template: copy the *contents* of the template repo's
   `template/` folder (including the hidden `.claude/` folder) into the
   project root, using the install command in the template repo's
   README.
3. Run `/setup` once.

**What it does:**

1. Checks that the project has its technical files, and stops if it
   doesn't.
2. Looks at the project's files to learn what it is and how it runs,
   including any `CLAUDE.md` or design docs the project already has.
3. Asks you about the app in three short rounds: what it does, its data
   and rules, and its shape and scope. Then a fourth round suggests how
   the app should be tested, based on what it is. It checks your answers
   against the code and points out where they differ. Then it shows you
   a summary to confirm.
4. Sets up testing: creates the `tests/` folder and its `README.md`, and
   installs the test tools you chose, after your go-ahead.
5. Writes `docs/architecture.md`, a snapshot of the design used for
   orientation, and `docs/project-phase-plan.md`, the high-level
   blueprint of what gets built in which phase.
6. Writes `CLAUDE.md`, using `claude-template.md` as its layout. An old
   `CLAUDE.md` is replaced, but its useful information is carried over.
7. Tells you what it did and what to do next.

It installs only the test tools you approve, and doesn't run or commit
anything.

## Steps

Do these in order. Write every message to the user in plain language:
no jargon, or explain it in a few words when it can't be avoided.

### 1. Check the template is there

If `claude-template.md` doesn't exist in the project root, tell the user
the template folder wasn't copied in fully, and stop.

### 2. Check the project has its technical files

List the project root, including hidden files. If it holds nothing but:

- template files: `.claude/`, `claude-context/`, `claude-template.md`
- harmless extras: `.git/`, `.DS_Store`, `README.md`, `.gitignore`,
  `LICENSE`, an empty `docs/`

then the project hasn't been scaffolded yet. Tell the user to scaffold it
first: run the scaffold command for their project type from
`scaffolding.md` in the template repo (it creates a new project folder),
install the template into that folder, and run `/setup` there. Then stop.

### 3. Look at the project

Read the files that show what the project is and how it runs. Start with
the existing `CLAUDE.md`, `docs/architecture.md` and
`docs/project-phase-plan.md`, if there are any, then for example:
`README.md`, dependency files (`package.json`, `pyproject.toml`,
`requirements.txt`, `Gemfile`, `go.mod`, …), CI settings
(`.github/workflows/`), `docker-compose.yml`, the database schema and
migration folders, the main source folders, the test folders and their
shared helpers (for *Tests* in `CLAUDE.md`), `.gitignore`. Note any test
tools already set up: `vitest`, `jest`, `@playwright/test` or `pytest`
in the dependency files, and config files such as `vitest.config.*`,
`jest.config.*`, `playwright.config.*`, `pytest.ini` or a
`[tool.pytest.ini_options]` table in `pyproject.toml`. Look at any
images in `claude-context/ui-prototypes/` too: they can suggest answers
about screens, users and the core flow. Don't change anything yet.

Note how much the project already does beyond its starter files. This
decides how much weight the code carries in the interview.

### 4. Interview the user

The goal is to learn five things well enough to write
`docs/architecture.md`, `docs/project-phase-plan.md` and `tests/README.md`:
the business logic, the data model, the high-level architecture, the
phases, and how the app is tested.

Ask the four rounds below, one round per message, and wait for the
answers before the next round. Use earlier answers to make later questions
concrete (e.g. name the entities you heard about in round 1). The user
can say "skip" to any question; note skipped questions for *Open
questions*.

**Use the code.** Offer a suggested answer wherever the code or the
existing docs show one, so the user can reply "yes".

- When the project is mostly starter files, the code shows little about
  the business, so the answers are the main source for the design.
- When the project already has real work in it, check each answer
  against the code. Where they differ, say what the code shows and ask
  which is right. If the code is right, use it. If the answer describes
  what is intended but not built yet, use the answer and list the
  difference under *Open questions*. Never settle a difference silently.

**Round 1: The app**

1. What's the project called, and what will the app do? What problem does
   it solve?
2. Who will use it? List the types of users (e.g. customer, staff, admin),
   what each one can do, and whether they need to sign in.
3. Walk through the most important thing a user does, step by step, from
   start to finish.

**Round 2: Data and rules**

4. What things does the app keep track of, and how are they connected?
   (e.g. a customer has one or more reservations, and each reservation is
   for one table) Mention the key details of each, such as a
   reservation's date, time and party size.
5. What rules does the app follow? Think of limits, statuses, prices,
   deadlines and who may do what. (e.g. a reservation can be cancelled up
   to 24 hours before)
6. Does the app store anything sensitive, such as personal details,
   payments or health data?

**Round 3: Shape and scope**

7. Where will people use it: in a web browser, as a phone app, both, or
   only as an API or script? (If it has screens: a few mockups or
   screenshots can go in `claude-context/ui-prototypes/` at any time,
   and features that touch the UI will follow them.)
8. Does it need outside services, such as payments, email or SMS, maps,
   AI, or sign-in with Google?
9. What phase is the project in now, and what must that phase include?
   What comes after it, and what can wait for later? (For a project with
   real work in it, suggest a first phase that sums up what's built.)

**Round 4: Testing**

10. Suggest how to test the app, then ask: "Here's how I suggest testing
    <app>. Keep it, change a tool, or drop a layer?"

Build the suggestion from the code and the answers to rounds 1–3 (does
it have screens, an API, a database, outside services?). Show it as a
small table with one row per layer: **Layer**, **What it checks** (in
plain words, with an example from this app, e.g. "a booking for 9 people
is refused"), and **Tool**. The layers:

- **Unit** — always. Fast tests of single pieces of logic: business
  rules, calculations, validation.
- **Integration** — when there is an API, a database or an outside
  service: an endpoint or a database query working end to end inside the
  app.
- **End-to-end (e2e)** — when the app has screens: a real user flow,
  clicked through in a browser or on a phone, e.g. the core flow from
  round 1.

Suggested tools per project type:

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

- With a database: integration tests use a separate test database, or
  undo their changes after each test, and never touch real data.
- With outside services (payments, email, AI, …): tests never call them
  for real; they use stand-ins kept in `tests/helpers/`.

Test tools already in the project win: suggest those, say so, and don't
suggest switching. Unit tests can't be dropped; the other layers can.

Ask follow-up questions only where an answer leaves the data model or the
core flow unclear. Keep them few.

### 5. Play back and confirm

Show the user a short summary and ask them to confirm or correct it:

- the entities and how they connect, as a Mermaid `erDiagram`
- the core flow as numbered steps
- the tech stack, as found in the project's files
- testing: the layers, the tool for each, and the folders under `tests/`
- the phases: what the current phase includes, and what comes in which
  later phase
- any differences between the answers and the code, and how each was
  settled

Don't write anything until the user confirms. Apply corrections and play
back again if they change the data model or the phases.

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

   Put a `.gitkeep` in each empty folder. For pytest, the shared
   fixtures go in `tests/conftest.py` (pytest only finds them there),
   and `helpers/` holds the rest. If the project already keeps its tests
   elsewhere (e.g. `*.test.ts` files next to the code), keep that
   convention, create only what's missing, and say so.
2. **Write `tests/README.md`** from `testing-template.md` next to this
   file. Fill in every section, remove the layers that weren't chosen
   and the template comments, then check that no `{{` or `}}` is left.
   If a `tests/README.md` already exists, ask before replacing it.
3. **Show the install.** List the exact commands that add the chosen
   tools as development dependencies, e.g.
   `npm install -D vitest @testing-library/react jsdom @playwright/test`
   and `npx playwright install chromium`, or `uv add --dev pytest httpx`.
   Also list the config files and scripts you'll add: e.g.
   `vitest.config.ts` (include `tests/unit/**` and
   `tests/integration/**`), `playwright.config.ts` (`testDir:
   'tests/e2e'`, and `webServer` set to the command that runs the app,
   so the tests start it themselves), `testpaths = ["tests"]` under
   `[tool.pytest.ini_options]`, and `test` / `test:e2e` scripts in
   `package.json`. Skip tools that are already installed. Wait for a
   go-ahead: this is the only thing setup installs.
4. **Install and configure** after the go-ahead. Keep the configs
   minimal. If the user says no, keep the folders and `README.md`, and
   note the missing tools for step 10.

### 7. Write the design docs

Create two files from the templates next to this one. Fill in every
section from the confirmed answers and the code. Put anything skipped or
still unclear under *Open questions* instead of guessing. Remove the
template comments, then check that no `{{` or `}}` is left.

- `docs/architecture.md` from `architecture-template.md`.
- `docs/project-phase-plan.md` from `phase-plan-template.md`. Keep it a
  high-level blueprint: goals and scope per phase, no task lists.

If either file already exists, ask before replacing it, and carry over
anything in it that is still true.

### 8. Fill in CLAUDE.md

Go through every `{{placeholder}}` in `claude-template.md`. Fill in each
one the files or the confirmed answers answer clearly. Use real commands
and paths from the project. Don't guess. Sum up the *Overview* in
`docs/architecture.md` in one or two sentences for the description, and
take the stack from the project's files. Take the test commands under
*Checks* and the *Tests* section from step 6 and `tests/README.md`.

If there is an old `CLAUDE.md`:

- Use its information to fill in placeholders.
- Keep any rule or note from it that has no place in the template. Put it
  in the section where it fits best, or under *Other guidance*.
- If it disagrees with the project's files (e.g. a command that no longer
  exists), trust the files and note the difference for step 10.

Put every placeholder you couldn't fill into **one** short numbered list
of questions. Give a suggested answer wherever you have one, so the user
can reply "yes". Remove a line when it doesn't apply to the project, or
the user doesn't know the answer; note unanswered ones for step 10.

Remove a whole section only when it doesn't apply to the project (e.g.
*Database migrations* when there is no database). Never remove a section
because an answer is missing: other files refer to the headings by name.
If a kept section ends up with no lines, write "Not set yet." under its
heading and note it for step 10.

Always keep the *Source of truth* section, naming `docs/architecture.md`
and `docs/project-phase-plan.md`.

### 9. Write CLAUDE.md

Save `CLAUDE.md` in the project root, replacing the old one if there is
one. Remove every template comment, including the "how to use this
file" comment at the top. Then check that no `{{`, `}}` or `<!--` is
left anywhere in the file.

### 10. Report, then stop

Tell the user, in a short list:

1. That `CLAUDE.md` is ready, and what it now says the project is.
2. That `docs/architecture.md` holds the design and
   `docs/project-phase-plan.md` the phases, and which phase is current.
   They are a snapshot as of today. The code is the source of truth and
   will move ahead of them as features are built.
3. How testing is set up: the layers and tools, the `tests/` folders,
   that `tests/README.md` explains them, and what was installed. If the
   install was declined, the commands still to run.
4. Any differences between the answers and the code that were left under
   *Open questions*.
5. If there was an old `CLAUDE.md`: what was carried over, and anything
   left out because the project's files showed it was out of date.
6. Which sections were removed, and which questions were left
   unanswered, so they can add them later.
7. That `claude-template.md` isn't needed any more: delete it now, before
   committing. Don't delete it yourself.
8. If the app has screens: that 1–4 screenshots or wireframes in
   `claude-context/ui-prototypes/` will guide how features look. Say
   which ones are there already, if any.
9. What to do next:
   - After deleting `claude-template.md`, commit everything before
     anything else, including `tests/` and the new test configs; `/feature implement` stops if anything besides a
     spec is uncommitted. If the project
     isn't in a GitHub repository yet, commit everything on `main` (the
     exception in Team rule 1 of `claude-context/project-git-workflow.md`), then
     create the repository on GitHub and push `main`. Otherwise commit
     everything on a
     new branch (e.g. `setup`) and open a PR, following Part 1 of
     `claude-context/project-git-workflow.md`. Wait until a teammate has merged
     it, then `git checkout main` and `git pull --ff-only`: until then
     `main` doesn't have the skills or `CLAUDE.md`.
   - `/feature describe "<what you want>"` starts a new feature. Add
     `SURF` after `describe` for fewer approval stops; the default is
     `VERBOSE`.

<!--
Adding setup steps later: add each one as a new numbered step before
"Report, then stop", with a heading that says in plain words what it
does. Add a matching line to "What this does" at the top.
-->
