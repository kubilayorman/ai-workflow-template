# Claude Project Template 🚀

Start every project on the right foot with [Claude Code](https://claude.com/claude-code). Add this template to a new project and Claude gets clear instructions, solid design docs and a smooth, review-based team workflow from day one.

## What gets installed

Everything in [`template/`](template/) is copied into your project's root folder:

- **`claude-template.md`**: the layout for your project's `CLAUDE.md`, so Claude knows exactly how your project works.
- **`.claude/skills/`**: three slash commands that do the heavy lifting:
  - `/setup` learns about your app through a short interview, suggests how to test it (e.g. unit tests, plus Playwright end-to-end tests if it has screens), sets up a `tests/` folder and the tools you approve, then writes `CLAUDE.md`, `docs/architecture.md` and `docs/project-phase-plan.md`.
  - `/feature describe`, `/feature implement`, `/feature test` and `/feature finalize` take an idea from spec to finished code to tested code to pull request. Pick `VERBOSE` (default, approve every step) or `SURF` (fewer stops) when you start: `/feature describe SURF "<your idea>"`.
  - `/professorslughorn` keeps all the documentation up to date after features are merged: the design docs, `CLAUDE.md` when needed, and plain-language docs for non-technical and technical teammates alike, so everyone stays in the loop. It marks each merged feature's spec as `Documented`, so the next run knows what's new. 🤝
- **`claude-context/`**: guidelines for how Claude works and a simple git workflow for your team. Your feature specs and Project Overview docs live here too, and `ui-prototypes/` holds a few screenshots or wireframes your app's screens should follow.

## Getting started ✨

**Prerequisites:** [Claude Code](https://claude.com/claude-code), Git, and a scaffolded project. Not scaffolded yet? See [`scaffolding.md`](scaffolding.md).

1. From your project's root folder, install the template:

   ```bash
   tmp=$(mktemp -d)
   git clone --depth 1 https://github.com/kubilayorman/ai-workflow-template.git "$tmp"
   rsync -a --ignore-existing "$tmp/template/" .
   rm -rf "$tmp"
   ```

   Existing files are never overwritten.

2. Have mockups? Drop 1–4 screenshots or wireframes into `claude-context/ui-prototypes/` (optional, any time). Features that touch the UI follow them as blueprints.
3. Open Claude Code and run `/setup`.
4. Start building with `/feature describe "<your idea>"`.

## Updating

The install command skips files your project already has, so delete the old template files first, then run it again:

```bash
rm -rf .claude/skills/setup .claude/skills/feature .claude/skills/professorslughorn .claude/skills/project-overview
rm -f claude-context/ai-interaction.md claude-context/project-git-workflow.md
```

Your feature specs and Project Overview docs are left untouched. Afterwards, delete `claude-template.md` again: only `/setup` needs it.
