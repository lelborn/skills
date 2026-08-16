---
name: keep-a-changelog
description: >-
  Write, update, or review a project's CHANGELOG.md following the Keep a Changelog
  1.1.0 convention, with version numbers set by Semantic Versioning. Use this
  whenever you add a changelog entry, log a change, turn commits or a freeform
  list of changes into a changelog, decide whether a release is major/minor/patch,
  mark a release as yanked, or audit an existing CHANGELOG.md for spec compliance.
  Trigger on phrasings like "log this change", "what changed this release", "bump
  the version", or a pasted commit list — don't wait for the word "changelog".
  Cutting a release here means editing CHANGELOG.md (and proposing a version bump) —
  not creating git tags or GitHub releases unless the user explicitly asks.
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

Bump by the highest-impact change; the `semver` skill has the full logic and reasoning steps.

**At `1.0.0` and above** — the public API is a promise:

| Release contains… | Bump |
|---|---|
| Anything **Removed**, or a **Changed** entry that breaks compatibility | **MAJOR** |
| Any **Added**, or a **Deprecated**, entry (nothing breaking) | **MINOR** |
| Only **Fixed** / **Security** | **PATCH** |

**Under `0.y.z`** — no compatibility promise yet, so the whole scale slides down one slot (matching how npm `^` ranges resolve — see `semver`):

| Release contains… | Bump |
|---|---|
| Anything **Removed**, or a compatibility-breaking **Changed** entry | **MINOR** (`0.3.1` → `0.4.0`) |
| Only non-breaking entries — **Added** / **Changed** / **Deprecated** / **Fixed** / **Security** | **PATCH** (`0.3.1` → `0.3.2`) |

One breaking change sets the level for the whole release. State your reasoning when proposing a bump, and flag that a 0.x version carries no compatibility guarantee. Cut `1.0.0` when the API is stable and in production.

## Version source

Read the **current** version before proposing any bump — never invent one.

1. **`package.json` `version`** when the file exists — this is the source of truth for the next bump.
2. Otherwise the latest `## [x.y.z]` heading in `CHANGELOG.md`.
3. Otherwise another project manifest (`Cargo.toml`, `pyproject.toml`, …).
4. If none exist, ask — do not guess.

When you cut a release, propose updating `package.json` to match the new version if the file exists. Do not change it until the user confirms.

## What this skill does not do automatically

Updating the changelog is not permission to publish. **Never** create git tags, GitHub releases (`gh release create`), or push release commits unless the user explicitly asks — a push, merge, or "cut a release" in changelog terms does not imply any of that.

When a release looks ready, stop after the changelog (and version proposal) and ask whether they want you to:
- bump `package.json`
- commit
- tag
- push
- create a GitHub release

Do only what they confirm. If they say "just update the changelog", that is the whole job.

## Workflows

**Log a change:** classify it, add a one-line bullet under that type inside `## [Unreleased]` (create the section/heading if missing). No version or date yet.

**Cut a release:** read the current version from `package.json` (or the fallbacks in [Version source](#version-source)), compute the bump from the table above, then rename the `## [Unreleased]` contents into a new `## [x.y.z] - YYYY-MM-DD` section, leave a fresh empty `## [Unreleased]` above it, and update the link footer (`[unreleased]` → `compare/vNEW...HEAD`, add `[vNEW]`). Propose the matching `package.json` bump if applicable. Stop and ask before tagging, pushing, or creating a GitHub release — see [What this skill does not do automatically](#what-this-skill-does-not-do-automatically).

**From commits:** drop the noise (merges, formatting, pure refactors, docs/CI-only — the `chore`/`style`/`ci`/`build`/`test` types under Conventional Commits; see the `conventional-commits` skill), rewrite each surviving commit into a user-facing bullet, sort into the six types, propose the bump.

**Yanked release:** a release pulled after publishing is flagged in its heading and **kept, not deleted** (people who installed it need to know):

```markdown
## [1.4.1] - 2026-07-18 [YANKED]
```

## Auditing a CHANGELOG.md

Check: dates in `YYYY-MM-DD`; no raw commit/diff dumps; no empty type headings; versions strictly descending; preamble names both conventions; each version's bump matches what it changed (flag e.g. a removed feature bumped only as PATCH). Report a short list of concrete fixes, then apply them if the user wanted a fix rather than a review.
