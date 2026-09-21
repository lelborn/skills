---
name: gh-templates
description: Scaffold a repo's GitHub contribution templates — issue templates (bug/feature/chore + config) and a pull-request template — into `.github/`, following a Conventional-Commit-aligned house style. Adapts the project-specific bits (PR test-plan commands) to the actual repo instead of hardcoding them. Use when the user runs /gh-templates or asks to set up or add issue templates, a PR template, an ISSUE_TEMPLATE folder, a bug/feature report form, or `.github` contribution scaffolding. Creates `.github/` folders if missing, reuses them if present, and never overwrites an existing template without asking. (Dependabot config is a separate skill, gh-depbot.)
argument-hint: Optional — which to scaffold (issue, pr, all)
---

# gh-templates

Set up the files GitHub reads from `.github/` to shape issues and pull requests, following a house style aligned with Conventional Commits and this book's other skills. The canonical template bodies are in [references/templates.md](references/templates.md) — write those into the repo, adapting the marked project-specific lines. Don't reproduce another project's tooling verbatim.

## What it scaffolds

Core (the default when no argument narrows it):

- `.github/ISSUE_TEMPLATE/bug.md` — title prefix `fix: `, label `bug`
- `.github/ISSUE_TEMPLATE/feature.md` — title prefix `feat: `, label `enhancement`
- `.github/ISSUE_TEMPLATE/chore.md` — title prefix `chore: `, label `chore`
- `.github/ISSUE_TEMPLATE/config.yml` — `blank_issues_enabled: false`, optional `contact_links`
- `.github/PULL_REQUEST_TEMPLATE.md` — Summary / Changelog / Docs / Test plan / Notes

If the argument names a subset (`issue`, `pr`), do only that.

Dependabot is a separate skill: if the user also wants `.github/dependabot.yml`, hand off to **`gh-depbot`** rather than scaffolding it here.

## The house conventions (keep these; they're the point)

- **Issue titles carry a Conventional-Commit prefix** (`fix:`, `feat:`, `chore:`) so the type is set from the first keystroke. The chore template notes the title can switch to `docs:`/`ci:`/`refactor:`/`test:`/`build:` when one fits better.
- **Bodies are a spec, not a form.** HTML-comment instructions guide the writer; headings frame what to fill; a **Done when** checklist captures acceptance criteria; sections that can be empty (`Out of scope`, `Notes`, `Evidence`) are marked deletable.
- **The PR template threads to this book.** It reminds the author the title must be a Conventional Commit and points at the `conventional-commits`, `keep-a-changelog`, and `keep-it-human` skills; its **Changelog** section is a single `[Unreleased]`-ready line in the six Keep a Changelog types.

## Adapt to the project — don't hardcode

Before writing, read the repo so the project-specific lines fit:

- **PR Test plan.** Replace the placeholder checkboxes with the repo's real lint/test/build commands — from `package.json` scripts, a `Makefile`, or an existing `.github/workflows/*.yml`. If you can't determine them, leave a generic checklist with a `<!-- TODO -->` marker rather than inventing commands.
- **Labels.** The `labels:` in each issue template only apply if those labels exist in the repo (GitHub silently drops unknown ones). Note this, and offer to create them with `gh label create bug|enhancement|chore` — but only on explicit confirmation, since it changes repo settings.

## Where the files go

Ensure `.github/` and, for issue templates, `.github/ISSUE_TEMPLATE/` exist at the repo root — **create whichever is missing, reuse them if present.** The PR template sits directly in `.github/`.

## Don't clobber

If a target file already exists, **stop and ask** before overwriting — show what's there and offer to update it in place, add alongside (e.g. a second issue template), or skip it. Scaffolding is not permission to replace someone's existing templates.

## Related skills

Other `.github/` concerns have their own skills — don't duplicate them here: **`gh-protect`** for repository rulesets (`.github/.rulesets/`), and **`gh-depbot`** for `.github/dependabot.yml`.
