# /feature describe

## When this runs

Invoked as `/feature describe [VERBOSE|SURF] "<prompt>"`, e.g.
`/feature describe SURF "issue #12 — let users reset their password"`.
The optional first word is the mode (see *Modes* in `SKILL.md`); without
it, the mode is `VERBOSE`. The rest is the feature request.

## Confirm the mode, then start from the latest `main`

Run `git status`. If you're not on `main`, stop and ask the user what to
do. Otherwise, in one message, in both modes:

- state the mode: "Mode: VERBOSE (default)" or "Mode: SURF", with one
  line on what it means, and that they can reply with the other mode's
  name to switch
- show `git pull --ff-only`
- ask whether to continue

On a go-ahead, run the pull, so the spec is planned against the team's
latest code, including any PR merged since the last pull. If the user
names the other mode, use that one. If the pull fails, stop and point
the user to *Troubleshooting* in `claude-context/project-git-workflow.md`.

## Before drafting anything, read

1. `CLAUDE.md` and `claude-context/ai-interaction.md` — working rules.
2. The design docs `CLAUDE.md` lists under *Source of truth* — a
   snapshot of the domain model, design, naming, and what's planned.
   `claude-context/Project Overview/` for orientation, if useful. Both can lag;
   the code is always the source of truth.
3. The GitHub issue, if the prompt names one. Check it against the code:
   fields, paths, commands or config keys that don't match the code are
   probably outdated issue text, not a design change.
4. The relevant source and test files, plus the migrations folder from
   `CLAUDE.md` if the feature touches the database — so the spec fits
   what already exists instead of reinventing it.
5. The images in `claude-context/ui-prototypes/`, if the feature adds or
   changes anything the user sees. There are only a few: open them all.
   They are blueprints for the app's look, not exact designs.

Skip re-reading files already read earlier in this same conversation.

## Steps

1. **Clarify first.** If the prompt leaves real decisions open (data
   model fields, commands, endpoints or screens, config keys, which part
   of the architecture it belongs to, whether it needs a schema change),
   ask before drafting. If the issue and the design docs disagree and
   the code doesn't settle it, ask. If it's unclear whether a screenshot
   in `ui-prototypes/` is meant for this feature's screen, ask. Ask only what actually
   changes the spec — no padding.
2. **Name the feature.** If the prompt names an issue, the feature name
   is the issue title (`gh issue view N`). Otherwise pick the most
   fitting short name from the prompt; if none is clear, ask the user.
   The PR title is built from this name.
3. **Draft the spec** and save it to `claude-context/<feature-name>-spec.md`,
   where `<feature-name>` is a short kebab-case slug built from the
   feature name. If the prompt names an issue, prefix the slug with the
   issue number (at least two digits, e.g. `07`, `12`, `123`) so spec, branch, and PR match (e.g.
   `12-password-reset`). The file must contain the five header lines
   followed by exactly these three sections, in this order, and nothing
   else:

   ```markdown
   **Feature name:** <feature name>
   **Issue:** <#N, or "none">
   **Status:** New
   **Mode:** <VERBOSE or SURF>
   **Test results:** not run yet

   # Feature functional description

   **In short:** <one sentence a non-developer can follow>

   <plain sentences on what the feature does>

   **Key terms:**
   - `<term>`: <one-line plain meaning>

   **How to see it working:**
   1. <step>, then <what you should see>

   # Feature technical requirements

   ### <path/to/file> (new | edit)
   *Why:* <one line, plain words>
   - <one instruction per bullet>

   ### Tests
   *Why:* <one line, plain words>
   - <layer>: <behavior to test>

   # Feature references and important notes

   **Decisions:**
   - <choice>: <why, and alternatives rejected>
   ```

   - **Status** — always `New` here. Later actions change it:
     `/feature implement` → `Implemented`, `/feature test` → `Tested`,
     `/feature finalize` → `PR created`, and after the merge
     `/professorslughorn` → `Documented`.
   - **Mode** — the mode confirmed at the start. Every later action
     reads it from here.
   - **Test results** — always `not run yet` here. `/feature test` fills
     it in.
   - **Functional description** — what the feature does and what the
     user sees/does, plain sentences, no implementation detail. For
     example: the page, endpoint or command they use, and the data that
     gets saved.
     - Open with an **In short:** line: one sentence a non-developer can
       follow.
     - When a project term (config key, flag, model field, issue number)
       has to appear, explain it in the same sentence or list it under
       **Key terms:** (optional, 3–6 entries, `term`: one-line plain
       meaning).
     - Always end it with a **How to see it working:** line: numbered
       steps to run, each with what the result should look like.
   - **Technical requirements** — concrete, bullet points, not prose.
     For example: files to create/edit, functions and signatures,
     business logic, model/schema changes and whether a migration is
     needed, commands/endpoints, config keys, dependencies to install,
     the logic worth testing (business rules, validation, edge cases;
     unit by default, integration or end-to-end only where *Tests* in
     `CLAUDE.md` calls for them; `/feature test` writes the tests, not
     `/feature implement`),
     any new technical debt the feature knowingly introduces.
     - Group the bullets under `###` subheadings, one per file or area.
       Dependencies, migrations and tests get their own subheading when
       present.
     - Start each group with one `*Why:*` line: what the change is for,
       in plain words a junior developer can follow.
     - One instruction per bullet. Keep signatures, exact error
       messages, constants and edge cases precise. Put code in code
       spans, and longer signatures in fenced blocks.
   - **References and important notes** — under these labels, using
     only the ones that apply:
     - **Decisions:** choices made while drafting, each with its reason
       and any alternatives rejected.
     - **Out of scope:** what's left out, and where it's handled
       instead.
     - **Reuse:** existing code, patterns or test fixtures to reuse or
       match, with their paths. If a screenshot in `ui-prototypes/` is
       named after the screen this feature builds, mention it here by
       name (e.g. "Login page follows `login-page.png` in
       `ui-prototypes/`"). Don't list screenshots that don't match, and
       don't describe the image: `/feature implement` looks at it.
     - **Issue/docs mismatches:** any issue-vs-docs mismatch and which
       side the spec follows, or where the feature goes against the
       design docs or the phase plan.
     - **Open questions.**
   - State each fact once. A technical bullet refers to a decision
     ("see Decisions") instead of repeating its reasoning.
   - The lists above are guidelines for what to cover, not a checklist —
     include what applies to this feature, skip what doesn't. The five
     header lines, the **In short:** line and the **How to see it
     working:** line are always required.
   - Keep it to the point: no marketing language, no filler, nothing
     obvious from the code restated. Written for an agent to execute;
     the **In short:**, *Why:* and **Key terms:** lines make it readable
     for a junior developer. Those lines only explain, they never add
     instructions.
   - Don't invent scope beyond the prompt — unresolved ambiguity goes
     under references as an open question, not a silent decision.
4. **Hand it off.** Tell the user the file was saved, give a one-line
   summary of what's in it, say which mode it carries (they can change
   the **Mode** line), and tell them to review/edit it, then run
   `/feature implement @claude-context/<feature-name>-spec.md` when ready, with
   the actual file name filled in.
5. **Stop.** Do not implement anything in this same turn, even if the
   user immediately says it looks good. Implementation only starts from a
   separate `/feature implement` invocation.
