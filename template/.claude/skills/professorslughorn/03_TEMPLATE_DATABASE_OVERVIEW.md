# {{Project Name}} — Database Overview

> **Generated:** {{YYYY-MM-DD}} · **Last updated:** {{YYYY-MM-DD}}
> **Scope:** {{schema, models, and migration folders covered}} · **Excluded:** {{...}}

<!--
TEMPLATE INSTRUCTIONS
- Action 2: runs after FILE_DESCRIPTIONS.md is written or updated in the same run.
- Use FILE_DESCRIPTIONS.md (especially the Business Actions Index) for where each table is created, changed, deleted and read. Confirm against the code.
- Follow 00_GENERATION_RULES.md and 01_LANGUAGE_GUIDE.md.
- HTML comments are guidance. Remove them from the final document.
-->

## At a Glance

<!-- What the data represents overall, in one plain paragraph. Then: database engine and hosting, the tool that defines tables and runs migrations, number of tables. -->

{{One paragraph a non-technical reader can follow.}}

| | |
|---|---|
| Database | {{PostgreSQL 16, hosted on ...}} |
| Schema defined in | {{ORM / migration tool, and folder}} |
| Number of tables | {{n}} |

## Entity Relationship Diagram

<!-- Every table and relationship. Over ~15 tables: show the main ones and note which are left out. -->

```mermaid
erDiagram
    {{CUSTOMERS}} ||--o{ {{ORDERS}} : places
```

## Tables

<!-- Repeat per table. Related tables together, main business tables first. -->

<a id="table-{{table_name}}"></a>
### `{{table_name}}`

**Purpose:** <!-- Why the table exists, in one sentence. --> {{...}}

**One row represents:** <!-- E.g. "one order placed by a customer". --> {{...}}

**Fields:**

<!-- Each field in business terms. Constraints (unique, default, allowed values) in the last column. -->

| Field | Type | Required | Description | Example / Constraints |
|---|---|---|---|---|
| `{{id}}` | {{uuid}} | Yes | {{...}} | {{Primary key}} |

**Keys and indexes:** <!-- Primary, foreign and unique keys. Indexes only if they have a clear reason; give it. --> {{...}}

**Relationships:** <!-- Plain language, then technical: "One customer has many orders (1:N via `orders.customer_id`)." --> {{...}}

**Lifecycle:**

<!-- Link each function to FILE_DESCRIPTIONS.md. "Never" where the app doesn't do that action. -->

| Action | Where | When |
|---|---|---|
| Created | [`{{function()}}`](FILE_DESCRIPTIONS.md#file-{{anchor}}) | {{Which user action or flow step}} |
| Changed | {{...}} | {{...}} |
| Deleted | {{...}} | {{Soft or hard delete? What else gets deleted with it?}} |
| Read | {{...}} | {{Which features use it}} |

**Status values:** <!-- Only for a status-like field: allowed values and how a row moves between them. State diagram if more than three states. Leave out otherwise. -->

```mermaid
stateDiagram-v2
    [*] --> {{pending}}
    {{pending}} --> {{paid}}
```

**Sensitive data:** <!-- Fields with personal or secret data. "None" if none. --> {{...}}

**Worth knowing:** <!-- Optional: misleading names, legacy fields, unused columns. --> {{...}}

---

## Relationships Summary

<!-- All relationships in one place. For each many-to-many, say what the linking table means in business terms. -->

| From | To | Type | Via | On delete |
|---|---|---|---|---|
| `{{customers}}` | `{{orders}}` | 1:N | `{{orders.customer_id}}` | {{Cascade / Restrict / Set null}} |

{{One sentence per many-to-many linking table explaining what it represents.}}

## Data Through the Core Flow

<!--
Rows are flow steps, columns are tables: created (C), read (R), updated (U) or deleted (D) at each step.
Step numbers follow "Core flow step numbers" in 00_GENERATION_RULES.md. Follow with two or three plain sentences on how data flows.
-->

| Step | `{{table_a}}` | `{{table_b}}` | `{{table_c}}` |
|---|---|---|---|
| 1. {{Customer signs up}} | C | | |
| 2. {{Customer places order}} | R | C | |

{{Plain-language summary of how data moves through the flow.}}

## Migrations and Schema Changes

<!-- Where migrations live, the exact commands to create and run one, and naming or ordering conventions. -->

{{...}}
