# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed

- **package.json** — renamed to `lelborn-skills` and trimmed to repo metadata only (removed npm `files` field) so `npx skills` resolves to the CLI, not this repo, when run from the project directory.

### Added

- **conventional-commits** — commit messages that follow Conventional Commits v1.0.0, including type selection, scopes, breaking changes, and changelog generation.
- **keep-a-changelog** — maintain a `CHANGELOG.md` following Keep a Changelog 1.1.0, with release cutting and compliance audits.
- **semver** — choose version bumps, validate version strings, and compare precedence using Semantic Versioning 2.0.0.
- **keep-it-human** — rewrite AI-sounding text so it reads naturally without changing meaning.
- **handoff** — compact a session into a handoff document for a fresh agent or session to continue from.
- **comment-code** — add constructive comments to source files, or audit existing comments read-only.
- **challenge** — stress-test a plan or decision with sharp questions and a sharpened outcome.
- **github-workflow** — govern every GitHub-touching action with pre/post-work gates: account resolution, issues, projects, milestones, branches, PRs, releases, and dependabot.
