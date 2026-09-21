# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed

- **package.json** — renamed to `lelborn-skills` and trimmed to repo metadata only (removed npm `files` field) so `npx skills` resolves to the CLI, not this repo, when run from the project directory.
- **semver** — pre-1.0 (`0.y.z`) bumps now follow the shifted convention (breaking → minor, feature/fix → patch) to match npm `^` range behaviour, and the decision reasons from consumer impact instead of defaulting new features to a minor bump.
- **keep-a-changelog** — 0.x version-picking now matches the shifted `semver` convention: a separate `0.y.z` bump table where non-breaking releases (including `Added`) are patch, not minor.

### Added

- **Prettier** — exact-version local install with `.prettierrc`, `.prettierignore`, `format` / `format:check` scripts, and workspace format-on-save via the official Prettier VS Code extension.
- **markdownlint** — workspace config using markdownlint's Prettier style so formatting rules (including hard tabs) are left to Prettier; unlabeled fences given a language.
- **.gitignore** — ignore OS junk, Node artefacts, secrets, and local skills CLI installs (`.agents`, `.claude`, `skills-lock.json`).
- **conventional-commits** — commit messages that follow Conventional Commits v1.0.0, including type selection, scopes, breaking changes, and changelog generation.
- **keep-a-changelog** — maintain a `CHANGELOG.md` following Keep a Changelog 1.1.0, with release cutting and compliance audits.
- **semver** — choose version bumps, validate version strings, and compare precedence using Semantic Versioning 2.0.0.
- **keep-it-human** — rewrite AI-sounding text so it reads naturally without changing meaning.
- **handoff** — compact a session into a handoff document for a fresh agent or session to continue from.
- **comment-code** — add constructive comments to source files, or audit existing comments read-only.
- **review-file** — deep review of one named file; reports findings and changes nothing until asked.
- **challenge** — stress-test a plan or decision with sharp questions and a sharpened outcome.
- **sc-init** — initialise a SuiteCloud Account Customization project with agent skills, Prettier/ESLint, and docs.
- **tooling-init** — set up a JavaScript/TypeScript project's Prettier, ESLint, and Playwright tooling with ignore rules, IDE settings, and project-aware package.json scripts, after evaluating the project.
- **gh-protect** — wizard that builds a GitHub repository ruleset (branch/tag/push) as JSON under `.github/.rulesets/`, then guides applying it via the UI or `gh api`; never changes repo settings unprompted.
- **gh-templates** — scaffold `.github/` issue templates (bug/feature/chore) and a pull-request template in a Conventional-Commit house style, adapted to the project.
- **gh-depbot** — configure `.github/dependabot.yml` from the repo's detected ecosystems, defaulting to weekly `chore` updates grouped by minor/patch.
- **gh-issues** — working standard for writing and managing GitHub issues: duplicates, scope, Conventional-Commit titles, a verifiable "Done when", and closing discipline.
- **gh-prs** — working standard for pull requests: branching, one logical change, Conventional-Commit titles, a real test plan, and merge discipline.
- **docs-init** — scaffold a `.docs/` tree (index, flat ops docs, one subfolder per unit kind) sized to the project, establishing the convention `doc-it` and `doc-check` maintain.
- **doc-it** — create or update one documentation page to match the repo's `.docs/` conventions, link to source, cross-link, and keep the index in sync.
- **doc-check** — audit a `.docs/` tree for leaked secrets/IDs, drift, broken links, and coverage gaps, reporting severity-ranked findings without changing anything.
- **archive-skill** — retire a skill to a tracked `.archive/` folder, delist it from the README, and log the change, without deleting it.
