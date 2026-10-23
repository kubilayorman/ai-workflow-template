# Updating the template in a project

Instructions for Claude, working in a project that has an older version of this template. The user asked you to clone this repo into a temporary folder outside the project; below it's called `$tmp`. The new template files are in `$tmp/template/`. Shell variables may not carry over between your commands, so write the folder's full path instead of `$tmp` in every command.

Write every message in plain language. Show each git command before you run it. Don't commit: the user reviews the changes first.

## 1. Check the starting point

1. `git status` in the project: the tree must be clean, so the diff shows only the update. If it isn't, ask the user to commit or stash their changes, and stop.
2. If `.claude/skills/feature/` doesn't exist, the template isn't installed. Point the user to *Getting started* in `$tmp/README.md`, and stop.
3. If the project is on GitHub: `git checkout main` and `git pull --ff-only`.
4. `git checkout -b update-workflow-template`.
5. If `$tmp` is a shallow clone (`git -C "$tmp" rev-parse --is-shallow-repository` prints `true`), run `git -C "$tmp" fetch --unshallow`. Step 3 needs the full history.

## 2. Sort the files

Every file in the project is in one of three groups:

| Group | Files | What to do |
|---|---|---|
| Template files | every file in `$tmp/template/` except `claude-template.md`, at the same path in the project | Replace them (step 3). |
| Files `/setup` wrote | `CLAUDE.md`, `docs/architecture.md`, `docs/project-phase-plan.md`, `tests/README.md` | Merge them (step 4). |
| The user's files | everything else, e.g. feature specs (`claude-context/*-spec.md`), `claude-context/Project Overview/`, images in `claude-context/ui-prototypes/`, skills that aren't in `$tmp/template/.claude/skills/` | Don't touch them. |

## 3. Replace the template files

For each template file:

- **Not in the project:** copy it in.
- **Same as the new version:** skip it.
- **Different:** check whether the user edited it, by comparing it with every past version of that file in `$tmp`:

  ```bash
  f="<path inside template/>"
  h=$(git hash-object "$f")
  git -C "$tmp" log --format=%H -- "template/$f" |
    while read c; do git -C "$tmp" show "${c}:template/$f" 2>/dev/null | git hash-object --stdin; done |
    grep -qx "$h" && echo "not edited" || echo "edited"
  ```

  - **Not edited:** replace it with the new version.
  - **Edited:** show the user the diff between their file and the new version, and point out the parts that look like their own changes. Ask which they want: the new version, the new version with their changes added back, or their file as it is.

Then remove files the template no longer has. List every file ever deleted from `template/`:

```bash
git -C "$tmp" log --no-renames --diff-filter=D --name-only --format= -- template/ | sort -u
```

For each path that isn't in `$tmp/template/` any more, delete the same path (without `template/`) from the project if it's there, e.g. `.claude/skills/project-overview/SKILL.md`. Remove folders this leaves empty.

## 4. Merge the files `/setup` wrote

These files mix the template's structure with the user's content. Compare each with its new template:

| Project file | New template |
|---|---|
| `CLAUDE.md` | `$tmp/template/claude-template.md` |
| `docs/architecture.md` | `$tmp/template/.claude/skills/setup/architecture-template.md` |
| `docs/project-phase-plan.md` | `$tmp/template/.claude/skills/setup/phase-plan-template.md` |
| `tests/README.md` | `$tmp/template/.claude/skills/setup/testing-template.md` |

- **Section the file lacks:** add it where the template has it, filled in from the code and the project's docs. If you can't fill it, write "Not set yet." under it.
- **Heading spelled differently:** use the template's spelling. Skills and docs find sections by their heading.
- **Template text** (text with no `{{placeholder}}`, e.g. the rules under *Source of truth* in `CLAUDE.md`): change it to the new wording.
- **The user's content** (what fills a placeholder, and anything they added): keep it word for word.
- **Section the template no longer has:** keep it, and mention it in the report.
- Don't copy template comments (`<!-- … -->`) into the file.

Skip a file that doesn't exist, and mention it in the report.

Before writing, show one table of every proposed change and wait for a go-ahead:

| File | Section | Change |
|---|---|---|
| `CLAUDE.md` | Checks | Adds the new heading, filled from `package.json` |

## 5. Clean up and report

1. `rm -rf "$tmp"`.
2. List the files grouped by what happened: added, replaced, deleted, merged (with the sections changed), kept as is (with the reason, e.g. "user kept their edits").
3. Tell the user what to do next:
   - Review the changes with `git diff`, then commit on this branch and open a PR, following Part 1 of `claude-context/project-git-workflow.md`. Commit before the next `/feature`: `/feature implement` stops if anything besides a spec is uncommitted.
   - If `claude-context/Project Overview/` has documents: once the PR is merged, run `/professorslughorn` on `main`. It adds any sections the new Project Overview templates introduced.
