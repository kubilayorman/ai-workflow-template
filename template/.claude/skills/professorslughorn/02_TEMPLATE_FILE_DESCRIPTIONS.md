# {{Project Name}} — File Descriptions

> **Generated:** {{YYYY-MM-DD}} · **Last updated:** {{YYYY-MM-DD}}
> **Scope:** {{folders covered}} · **Excluded:** {{folders or file types left out}}

<!--
TEMPLATE INSTRUCTIONS
- Action 1: runs first, built directly from the code.
- Follow 00_GENERATION_RULES.md (depth rule, update mode, cross-linking) and 01_LANGUAGE_GUIDE.md.
- HTML comments are guidance. Remove them from the final document.
-->

## How to Read This Document

<!-- How are the files ordered (by area or folder)? What was left out, and why? Keep the label table. -->

{{One or two sentences on how the document is ordered and what it covers.}}

| Label | Meaning |
|---|---|
| 🚪 | Entry point: a request, page, job, or command starts here |
| 🗄️ | Creates, changes, deletes, or reads database data |
| 🌐 | Calls an external service (payments, email, third-party API) |
| ⏱️ | Runs on a schedule or in the background |

## Business Actions Index

<!--
One row per main business entity: where is it created, changed and removed? Link each function to its section below.
Answers "where are customers created?" at a glance. Write "Never" if the app never does that action.
-->

| Entity | Created in | Changed in | Removed in |
|---|---|---|---|
| {{Customer}} | [`{{create_customer()}}`](#file-{{anchor}}) | {{...}} | {{...}} |

## File Index

<!-- Each documented file, its area, and one line on what it does, in the same order as the sections below. -->

| File | Area | Purpose |
|---|---|---|
| [`{{path/to/file}}`](#file-{{anchor}}) | {{Area}} | {{One line}} |

---

## {{Area or Folder Name}}

<!-- What does this group of files do together, in one or two sentences? -->

{{Group summary.}}

<a id="file-{{path-with-dashes}}"></a>
### `{{path/to/file}}`

**Labels:** {{🚪 🗄️ 🌐 ⏱️ — only those that apply}}

**Purpose:** <!-- One or two plain sentences. --> {{...}}

**Role in the app:** <!-- Page, API, business logic or background job? Which core flow step uses it (link it in PROJECT_OVERVIEW.md)? --> {{...}}

**Data touched:** <!-- Tables it creates, changes, deletes or reads, linked to DATABASE_OVERVIEW.md. "None" if none. --> {{...}}

**External effects:** <!-- Email, payments, outside APIs, files written. "None" if none. --> {{...}}

#### `{{function_name}}({{inputs}})`

<!-- Repeat for each function worth documenting in full (see depth rule). -->

- **What it does:** <!-- One sentence. --> {{...}}
- **Inputs:** <!-- In words: "the customer's email and chosen plan", not only types. --> {{...}}
- **Output:** <!-- What comes back, or the result. --> {{...}}
- **Side effects:** <!-- Rows saved, emails sent, status changed. "None" if none. --> {{...}}
- **Triggered by:** <!-- The user action, page, route, job or function that calls it, in business terms. --> {{...}}
- **Worth knowing:** <!-- Only business rules, limits or notable failure cases. Leave out if nothing. --> {{...}}

#### Class `{{ClassName}}`

<!-- Use instead of the function block for classes. -->

- **Represents:** <!-- The real-world thing or job it stands for. --> {{...}}
- **Key data it holds:** <!-- Only business-relevant attributes. --> {{...}}
- **Methods:**
  - `{{method_name}}({{inputs}})` — <!-- What it does, inputs, output, side effects. One or two sentences. --> {{...}}

**Notes:** <!-- Optional: misleading names, legacy behaviour, known problems. --> {{...}}

---

## Summarized Files

<!-- Helpers, utilities, setup, wiring: one line each, no function details. -->

| File | Purpose |
|---|---|
| `{{path/to/helper}}` | {{One line}} |
