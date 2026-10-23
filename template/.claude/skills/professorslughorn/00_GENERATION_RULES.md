# Documentation Generation Rules

## Purpose

The skill writes three documents that explain the project to two audiences:

- **Non-technical stakeholders**, who need to understand what the app does and what data it holds.
- **Junior developers**, who need to find their way around the code quickly.

Every document must be clear, short and accurate, and follow `01_LANGUAGE_GUIDE.md`.

## Output Files

All output goes in `claude-context/Project Overview/`, every run.

- Create the folder if it doesn't exist. Never write the documents anywhere else, even if a `docs/` folder exists.
- The name has a space: quote it in shell commands, `"claude-context/Project Overview/"`.
- The documents sit side by side, so cross-links are plain file names (e.g. `DATABASE_OVERVIEW.md#table-customers`).

| Order | Action | Output file | Template |
|---|---|---|---|
| 1 | `file-descriptions` | `claude-context/Project Overview/FILE_DESCRIPTIONS.md` | `02_TEMPLATE_FILE_DESCRIPTIONS.md` |
| 2 | `database-overview` | `claude-context/Project Overview/DATABASE_OVERVIEW.md` | `03_TEMPLATE_DATABASE_OVERVIEW.md` |
| 3 | `project-overview` | `claude-context/Project Overview/PROJECT_OVERVIEW.md` | `04_TEMPLATE_PROJECT_OVERVIEW.md` |

## Order and Dependencies

Every run writes all three in this order, each building on the earlier ones as they stand after this run:

1. **File Descriptions**, from the code: which functions create, change and delete data.
2. **Database Overview**, using File Descriptions for where each table is written and read.
3. **Project Overview**, summarizing both into the architecture and core data flow.

**Core flow step numbers** come from one source, used by all three documents. The first that applies wins:

1. The *Core flow* in `docs/architecture.md`, whether or not code exists.
2. Otherwise, *Data Through the Core Flow* in `DATABASE_OVERVIEW.md`, numbered fresh from the code.
3. Otherwise (no database), *Core Data Flow* in `PROJECT_OVERVIEW.md`.

Never take step numbers from a document written in an earlier run. Links from an earlier document to a later one (e.g. File Descriptions → flow steps) are added in the link pass after all three are written.

### Projects Without a Database

Still write all three documents:

- **`DATABASE_OVERVIEW.md`** keeps only the title, metadata header and **At a Glance**, which says there's no database and where the app keeps its data instead (files, browser storage, an external API, or nowhere).
- **`PROJECT_OVERVIEW.md`** sets the step numbers only if `docs/architecture.md` has no *Core flow*. Under *Database Changes* it writes "Not relevant at this point." instead of the link.
- **File Descriptions and Project Overview** leave out table links. 🗄️ marks code that reads or writes stored data, wherever it lives.

### Projects Without Code Yet

A new project may have `docs/architecture.md` and `docs/project-phase-plan.md` (from `/setup`) but little code. Still write all three documents:

- Take entities, fields, relationships, statuses, core flow and architecture from `docs/architecture.md`, and phases from `docs/project-phase-plan.md`. Mark anything without code **🛠️ Planned**.
- `DATABASE_OVERVIEW.md` describes planned entities as tables, with the *Lifecycle* "Where" column `🛠️ Planned` until code exists.
- `FILE_DESCRIPTIONS.md` describes only files that exist, such as scaffolded starter files.
- Reuse the step numbers from `docs/architecture.md`.

## Update Mode

The skill updates existing documents; it doesn't rewrite them. A missing document is created in full from its template.

For an existing document:

1. Compare every section against the files on disk, which match the latest `main` (Step 0 in `SKILL.md`). Don't use git history to decide what changed.
2. A section is affected if anything it describes no longer matches the code, including added, removed or renamed files, functions and tables.
3. Compare the document's headings with its template. This catches template changes from a template update. Add a section the document lacks, written from the code. Rename a heading the template spells differently. Keep a section the template no longer has, and mention it in the report.
4. Rewrite only affected sections. Keep the rest word for word.
5. Never change text between `<!-- manual:start -->` and `<!-- manual:end -->`; people write these by hand.
6. Keep each `⚠️ Needs confirmation` marker until the code or the user answers it.
7. Fix cross-links to renamed or removed items.
8. Update the last updated date only if a section changed. Otherwise leave the document untouched, header included: a new date alone isn't a change.
9. Tell the user what changed, in a short list.

## Depth Rule: Focus on Business Value

Document code by how much it explains what the app does with its data, not by how much code there is.

**The test:** would a product owner or new developer need this to understand how the app handles business data? If yes, document it in full; if not, one line or nothing. When unsure, ask: does it change what happens to a customer, an order, or another business entity?

**Document in full:**

- Code that creates, changes or deletes a business entity (e.g. where a Customer is created).
- Code that applies a business rule (pricing, eligibility, status changes, limits).
- Entry points: routes, pages, scheduled jobs, event handlers, CLI commands.
- Code that talks to an external service (payments, email, third-party APIs).
- Each step of the core data flow.

**One line:** helpers, utilities, framework setup and wiring.

**Leave out:** tests, generated, vendored or third-party code, build output, imports, and plumbing that doesn't change what the business sees. Describe *what* happens to the data, not *how* the code is wired.

## Accuracy

- Base every statement on the code. Don't guess business intent; write `> ⚠️ Needs confirmation: <question>` instead.
- Never include secret values, passwords, keys or real personal data. Environment variable *names* are fine.
- Don't describe features, files or tables that don't exist, except items `docs/architecture.md` plans, marked **🛠️ Planned**. Where code and `docs/architecture.md` both describe something, the code wins.
- Remove a 🛠️ Planned mark as soon as the code has the item.

## Cross-Linking

Use explicit anchors so links stay stable, on the line directly above the heading they belong to:

| Item | Anchor format | Example |
|---|---|---|
| File | `file-<path>` with `/` and `.` replaced by `-` | `<a id="file-src-services-customer-py"></a>` |
| Table | `table-<table_name>` | `<a id="table-customers"></a>` |
| Core flow step | `flow-step-<number>` | `<a id="flow-step-3"></a>` |

Link like `[customers](DATABASE_OVERVIEW.md#table-customers)`. Required links:

- File Descriptions → the tables each file touches and the flow steps it takes part in.
- Database Overview → the files that create, change, delete and read each table.
- Project Overview → the files and tables for each core flow step.

## Metadata Header

Every document starts with this header, directly under the title:

```markdown
> **Generated:** YYYY-MM-DD · **Last updated:** YYYY-MM-DD
> **Scope:** <folders covered> · **Excluded:** <folders or file types left out>
```

## Diagrams

All diagrams are Mermaid, in a ```` ```mermaid ```` block, readable without zooming. Over ~15 boxes, split the diagram or show only the main parts.

## Final Check Before Saving

- [ ] Every template section is filled in, or marked `⚠️ Needs confirmation`.
- [ ] The writing follows `01_LANGUAGE_GUIDE.md`.
- [ ] All cross-links point to existing anchors.
- [ ] The metadata header is updated if a section changed, and untouched otherwise.
- [ ] Unchanged and manual sections are untouched.
