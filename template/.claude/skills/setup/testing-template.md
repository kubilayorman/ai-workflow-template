<!--
TEMPLATE — layout for tests/README.md
Used by /setup. Fill every {{placeholder}} from the testing confirmed in the interview and the project's files.
- Keep only the layers that were chosen: delete the rows and sections of the others.
- Use real commands, paths and file names from the project. Never guess.
- Keep the section headings: CLAUDE.md and /feature test refer to this file.
- Delete this comment and every other template comment.
-->

# Tests

How this project is tested: which kinds of tests there are, where they live, and how to run them.
Tests are written by `/feature test`, after a feature is built.

## Layers

| Layer | What it checks | Tool | Folder |
|---|---|---|---|
| Unit | Single pieces of logic, on their own: {{e.g. "a booking for more than 8 people is refused"}} | {{e.g. Vitest}} | `tests/unit/` |
| Integration | {{e.g. "An API endpoint and the database working together: creating a booking saves it"}} | {{e.g. Vitest + Supertest}} | `tests/integration/` |
| End-to-end | {{e.g. "A real user flow, clicked through in a browser: booking a table from start to finish"}} | {{e.g. Playwright}} | `tests/e2e/` |

## How to run

<!-- One line per layer. Say what has to be running first, if anything. -->

- **Unit and integration:** `{{e.g. npm test}}` {{— needs: e.g. "the test database running (docker compose up -d)"}}
- **End-to-end:** `{{e.g. npm run test:e2e}}` {{— e.g. "starts the app by itself; first time only: npx playwright install chromium"}}

## Naming

<!-- One line per layer, following the tool's default pattern. -->

- Unit: {{e.g. `tests/unit/<feature>.test.ts` / `tests/unit/test_<feature>.py`}}
- Integration: {{e.g. `tests/integration/<area>.test.ts`}}
- End-to-end: {{e.g. `tests/e2e/<flow>.spec.ts`, one file per user flow}}

## What's worth testing

Test only what could break without anyone noticing. Skip code that only passes data along, sets up
config or layout, or calls a framework or library without logic of its own.

- **Unit (the default):** business rules, calculations, validation, permissions, status changes,
  edge cases and error handling.
- **Integration:** a feature that adds or changes an API endpoint, a database query, or the way the
  app talks to an outside service.
- **End-to-end:** only a feature that adds or changes a core user flow from `docs/architecture.md`.
  Few and slow: one test per flow, covering the main successful path.

## Shared helpers and fixtures

- `tests/fixtures/` — shared test data: {{e.g. sample users, request bodies, database records}}.
- `tests/helpers/` — shared setup: {{e.g. "`setup.ts` (loaded by Vitest before every test)" / "helpers used by `tests/conftest.py`"}}.
- {{Outside services: e.g. "Tests never call Stripe or the email service for real; use the stand-ins in `tests/helpers/`."}}
- {{Database: e.g. "Integration tests use the separate test database and undo their changes after each test; never assume it's empty."}}
