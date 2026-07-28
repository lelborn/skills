---
name: keep-a-changelog
description: Write, update, or review a project's CHANGELOG.md following the Keep a Changelog 1.1.0 convention, with version numbers set by Semantic Versioning. Use this whenever you add a changelog entry, log a change, cut a release, turn commits or a freeform list of changes into a changelog, decide whether a release is major/minor/patch, mark a release as yanked, or audit an existing CHANGELOG.md for spec compliance. Trigger on phrasings like "log this change", "what changed this release", "bump the version", or a pasted commit list — don't wait for the word "changelog".
---

# Keep a Changelog

Maintain `CHANGELOG.md` files that follow [Keep a Changelog 1.1.0](https://keepachangelog.com/en/1.1.0/), with versions set by Semantic Versioning (the companion `semver` skill has the full bump rules). A changelog exists so a human can see what changed at a glance — which is why entries are written for people (not dumped from `git log`), grouped by kind, and newest-first.

## Core principles

- Written for **humans** — rewrite commits/diffs into plain, user-facing prose; never paste a raw git log.
- Every released version gets an entry, **newest first**, dated `YYYY-MM-DD`.
- An **`[Unreleased]`** section at the top collects changes before they're versioned.
- Group changes by the six types below; omit any type heading with no entries.
- The preamble names both Keep a Changelog and Semantic Versioning.

## File skeleton

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.2.0] - 2026-07-20

### Added

- User-facing description of a new feature.

### Fixed

- User-facing description of a bug fix.

[unreleased]: https://github.com/OWNER/REPO/compare/v1.2.0...HEAD
[1.2.0]: https://github.com/OWNER/REPO/compare/v1.1.0...v1.2.0
```

The link footer turns each version heading into a diff link. Build the compare URLs from the project's remote; omit the footer rather than guess a URL.

## The six types

In this order when several apply: **Added** (new features), **Changed** (changes to existing behaviour), **Deprecated** (still present, slated for removal), **Removed** (taken out this release), **Fixed** (bug fixes), **Security** (vulnerability fixes — list separately even if it duplicates a Fixed entry). Each bullet is one short line about what changed and why a user cares, not the implementation detail.

## Picking the version

Bump by the highest-impact change (the `semver` skill has the full logic):

| Release contains… | Bump |
|---|---|
| Anything **Removed**, or a **Changed** entry that breaks compatibility | **MAJOR** |
| Any **Added**, or a **Deprecated**, entry (nothing breaking) | **MINOR** |
| Only **Fixed** / **Security** | **PATCH** |

One breaking change makes the whole release MAJOR. Below `1.0.0`, treat would-be MAJOR bumps as MINOR until the user declares `1.0.0`. If the current version is unknown, read it from the latest `## [x.y.z]` heading or a version field (`package.json`, `Cargo.toml`, …) rather than inventing one; state your reasoning when proposing a bump.

## Workflows

**Log a change:** classify it, add a one-line bullet under that type inside `## [Unreleased]` (create the section/heading if missing). No version or date yet.

**Cut a release:** rename the `## [Unreleased]` contents into a new `## [x.y.z] - YYYY-MM-DD` section (version from the table above), leave a fresh empty `## [Unreleased]` above it, and update the link footer (`[unreleased]` → `compare/vNEW...HEAD`, add `[vNEW]`).

**From commits:** drop the noise (merges, formatting, pure refactors, docs/CI-only — the `chore`/`style`/`ci`/`build`/`test` types under Conventional Commits; see the `conventional-commits` skill), rewrite each surviving commit into a user-facing bullet, sort into the six types, propose the bump.

**Yanked release:** a release pulled after publishing is flagged in its heading and **kept, not deleted** (people who installed it need to know):

```markdown
## [1.4.1] - 2026-07-18 [YANKED]
```

## Auditing a CHANGELOG.md

Check: dates in `YYYY-MM-DD`; no raw commit/diff dumps; no empty type headings; versions strictly descending; preamble names both conventions; each version's bump matches what it changed (flag e.g. a removed feature bumped only as PATCH). Report a short list of concrete fixes, then apply them if the user wanted a fix rather than a review.
