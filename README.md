# AI Workflow Template 🚀

Start every project on the right foot with [Claude Code](https://claude.com/claude-code). Add this template to a new project and Claude gets clear instructions, solid design docs and a review-based team workflow from day one.

## What gets installed

Everything in [`template/`](template/) is copied into your project's root folder:

- **`claude-template.md`**: the layout for your project's `CLAUDE.md`, so Claude knows how your project works.
- **`.claude/skills/`**: three slash commands:
  - `/setup` interviews you about your app, suggests how to test it (e.g. unit tests, plus Playwright end-to-end tests if it has screens), sets up `tests/` and the tools you approve, then writes `CLAUDE.md`, `docs/architecture.md` and `docs/project-phase-plan.md`.
  - `/feature describe`, `implement`, `test` and `finalize` take an idea from spec to tested code to pull request. Pick `VERBOSE` (default, approve every step) or `SURF` (fewer stops): `/feature describe SURF "<your idea>"`.
  - `/professorslughorn` updates all documentation after features are merged — the design docs, `CLAUDE.md` when needed, and plain-language docs for technical and non-technical teammates — and marks each merged spec `Documented`. 🤝
- **`claude-context/`**: how Claude works, a simple git workflow for your team, your feature specs and Project Overview docs, and `ui-prototypes/` for screenshots your screens should follow.

## Getting started ✨

**Prerequisites:** [Claude Code](https://claude.com/claude-code), Git, and a scaffolded project. Not scaffolded yet? See [`scaffolding.md`](scaffolding.md).

1. From your project's root folder, install the template (existing files are never overwritten):

   ```bash
   tmp=$(mktemp -d)
   git clone --depth 1 https://github.com/kubilayorman/ai-workflow-template.git "$tmp"
   rsync -a --ignore-existing "$tmp/template/" .
   rm -rf "$tmp"
   ```

2. Optional, any time: drop 1–4 screenshots or wireframes into `claude-context/ui-prototypes/`. UI features follow them as blueprints.
3. Open Claude Code and run `/setup`.
4. Start building with `/feature describe "<your idea>"`.

## Updating

Open Claude Code in your project and paste:

```
Clone https://github.com/kubilayorman/ai-workflow-template into a temporary folder outside this project, then follow its UPDATING.md to update this project.
```

Claude works on a new branch and doesn't commit, so you can review every change. It handles three kinds of files:

- **Template files** (the skills, `ai-interaction.md`, `project-git-workflow.md`): replaced with the new version. If you changed one, Claude asks whether to keep your changes.
- **Files `/setup` wrote** (`CLAUDE.md`, `docs/architecture.md`, `docs/project-phase-plan.md`, `tests/README.md`): new sections and wording from the new templates are added. Your content stays.
- **Your own files** (feature specs, Project Overview docs, screenshots, your own skills): not touched.

Once the update is merged, run `/professorslughorn` on `main` to add any new sections to your Project Overview docs.
