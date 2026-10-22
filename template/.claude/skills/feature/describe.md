# /feature describe

## When this runs

Invoked as `/feature describe [VERBOSE|SURF] "<prompt>"`, e.g. `/feature describe SURF "issue #12 — let users reset their password"`. The mode is parsed as in *Routing rule* in `SKILL.md`.

## Confirm the mode, then start from the latest `main`

Run `git status`. If you're not on `main`, stop and ask what to do. Otherwise, in one message (both modes):

- state the mode — "Mode: VERBOSE (default)" or "Mode: SURF" — with one line on what it means, and that they can reply with the other mode's name to switch
- show `git pull --ff-only`
- ask whether to continue

On a go-ahead (or the other mode's name, which switches it), run the pull, so the spec fits the team's latest code. If it fails, stop and point to *Troubleshooting* in `claude-context/project-git-workflow.md`.

## Before drafting, read

Skip files already read in this conversation.

1. `CLAUDE.md` and `claude-context/ai-interaction.md`.
2. The design docs under *Source of truth* in `CLAUDE.md`, and `claude-context/Project Overview/` if useful. Both can lag behind the code.
3. The GitHub issue, if the prompt names one. Fields, paths, commands or config keys that don't match the code are probably outdated issue text, not a design change.
4. The relevant source and test files, and the migrations folder if the feature touches the database.
5. If the feature changes anything the user sees: all images in `claude-context/ui-prototypes/` — blueprints for the look, not exact designs.

## Steps

1. **Clarify first.** Before drafting, ask only what changes the spec:
   - real open decisions: data model fields, commands, endpoints or screens, config keys, where it fits in the architecture, whether it needs a schema change
   - the issue and design docs disagree and the code doesn't settle it
   - it's unclear whether a `ui-prototypes/` screenshot is meant for this feature's screen

2. **Name the feature.** With an issue, use its title (`gh issue view N`). Otherwise pick a short name from the prompt, or ask.

3. **Draft the spec** in the *Spec format* below, saved as `claude-context/<feature-name>-spec.md` (a short kebab-case slug). With an issue, prefix its number, at least two digits (`07`, `12`, `123`), so spec, branch and PR match: `12-password-reset`.

4. **Hand it off.** Give a one-line summary of the saved spec and its mode (editable in the **Mode** line). Tell the user to review it, then run `/feature implement @claude-context/<feature-name>-spec.md` (real file name).

5. **Stop.** Don't implement anything in this turn, even if the user says it looks good.

## Spec format

Five header lines, then exactly these three sections, in order, and nothing else:

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

### Header lines

**Status** starts at `New` (see *Lifecycle* in `SKILL.md`), **Mode** is the confirmed mode, and **Test results** stays `not run yet` until `/feature test`.

### Functional description

What the user sees and does, with no implementation detail.

- Explain any project term (config key, flag, model field) in the same sentence, or under **Key terms:** (optional, 3–6 entries).
- **How to see it working:** gives numbered steps, each with the expected result.

### Technical requirements

Concrete bullets, not prose. Cover what applies: files, functions and signatures, business logic, schema changes and whether a migration is needed, commands/endpoints, config keys, dependencies, technical debt knowingly added, and the logic worth testing (unit by default; other layers only where *Tests* in `CLAUDE.md` calls for them).

- One `###` subheading per file or area; dependencies, migrations and tests get their own.
- Each group opens with a `*Why:*` line a junior developer can follow.
- One instruction per bullet. Keep signatures, error messages, constants and edge cases exact; code in code spans, long signatures in fenced blocks.

### References and important notes

Use only the labels that apply:

- **Decisions:** choices made while drafting, with reasons and rejected alternatives.
- **Out of scope:** what's left out, and where it's handled.
- **Reuse:** existing code, patterns or fixtures, with paths. Name a `ui-prototypes/` screenshot only if it's named after this feature's screen (e.g. "Login page follows `login-page.png`"); don't describe it.
- **Issue/docs mismatches:** which side the spec follows, or where it departs from the design docs or phase plan.
- **Open questions.**

### Writing rules

- State each fact once. A technical bullet points to a decision ("see Decisions") instead of repeating its reasoning.
- The lists above are guidelines, not a checklist. The five header lines, **In short:** and **How to see it working:** are always required.
- No marketing language, filler, or restating the code. **In short:**, *Why:* and **Key terms:** only explain; they never add instructions.
- Don't invent scope. Unresolved ambiguity goes under **Open questions**, not into a silent decision.
