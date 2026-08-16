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
