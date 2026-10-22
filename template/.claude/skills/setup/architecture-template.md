<!--
TEMPLATE — layout for docs/architecture.md
Used by /setup. Fill every {{placeholder}} from the confirmed answers and the code.
- Keep the section headings: /professorslughorn maps them onto PROJECT_OVERVIEW.md and DATABASE_OVERVIEW.md.
- Use the users' business words for entities and steps; these names carry into code, database and later docs. If the code already names an entity, use that name and mention the business word in its Purpose line.
- Never guess. Unanswered items go under "Open questions".
- Delete this comment and every other template comment.
-->

# {{Project name}} — Architecture

> **Status:** {{Planned / Partly built / Built}} · **Last updated:** {{YYYY-MM-DD}}

A snapshot of the domain model, design, tech stack and naming as of the date above, plus what's planned but not built. The code is always the source of truth and moves ahead of this document.

## Overview

{{Two or three sentences: what the app does, who it is for, and what problem it solves.}}

## Users and roles

<!-- One row per type of user. -->

| Role | Who they are | What they can do | Signs in? |
|---|---|---|---|
| {{Customer}} | {{...}} | {{...}} | {{Yes / No}} |

## Domain model

{{One paragraph: what the data represents overall.}}

```mermaid
erDiagram
    {{CUSTOMER}} ||--o{ {{RESERVATION}} : makes
```

<!-- Repeat this block for each entity. Main business entities first. -->

### {{Entity name}}

**Purpose:** {{Why it exists, one sentence.}}

**Key details:**

| Field | Meaning | Required | Example / Rules |
|---|---|---|---|
| {{date}} | {{...}} | {{Yes}} | {{...}} |

**Relationships:** {{Plain language, e.g. "One customer has many reservations."}}

**Statuses:** {{Allowed values and how an item moves between them, or leave this line out.}}

### Sensitive data

{{Which details are personal, payment or otherwise sensitive, and why they are needed. "None" if none.}}

## Business rules

<!-- One line per rule. Limits, deadlines, prices, permissions, status changes. -->

- {{e.g. A reservation can be cancelled up to 24 hours before its start time.}}

## Core flow

{{One sentence: what the main flow achieves.}}

<!-- The main successful path only. /professorslughorn reuses these step numbers. -->

1. **{{Step name}}:** {{Who does what, and which entities are created, read, changed or deleted.}}
2. **{{Step name}}:** {{...}}

**Result:** {{What the user or business has at the end.}}

## Architecture

{{One paragraph: the main parts of the app, what runs where, and how the parts talk to each other.}}

```mermaid
flowchart LR
    {{User}} --> {{Frontend}} --> {{API}} --> {{Database}}
```

- **{{Part}}:** {{What it does.}}

### External services

<!-- Write "None" if the app uses none. -->

| Service | Used for |
|---|---|
| {{Stripe}} | {{Taking payments}} |

## Tech stack

| Part | Choice | Why |
|---|---|---|
| Project type | {{e.g. Next.js (web app), Python API (FastAPI), Mobile app (Expo / React Native), or "Other"}} | {{...}} |
| {{Frontend / Backend / Database / Hosting}} | {{...}} | {{...}} |
| Testing | {{Layers and tools, e.g. "Unit: Vitest · End-to-end: Playwright"; details in `tests/README.md`}} | {{...}} |

## Phases

What gets built in which order is in `docs/project-phase-plan.md`.

## Open questions

<!-- Questions skipped or left unclear during setup. Write "None" if none. -->

- {{...}}
