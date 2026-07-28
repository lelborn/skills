---
name: semver
description: Apply Semantic Versioning 2.0.0 (semver.org) to decide version bumps, validate version strings, and compare version precedence. Use whenever a task involves choosing the next version number, judging whether a change is a MAJOR/MINOR/PATCH bump, checking that a string is a well-formed version, ordering versions by precedence, or building a pre-release or build-metadata version. Often used as a building block by release, changelog, and publishing workflows — trigger whenever version numbers are being reasoned about, even if "semver" is never said.
---

# Semantic Versioning (SemVer 2.0.0)

Decision logic and reference for [SemVer 2.0.0](https://semver.org/). Often consumed by other workflows (the `keep-a-changelog` and `conventional-commits` skills) that just need the bump and the reason, so keep answers terse.

A version is a compatibility promise about a **declared public API**: MAJOR = breaks you, MINOR = new but compatible, PATCH = compatible fixes. Without a declared public API "breaking" is undefined — establish it or ask before deciding.

## Format

```
MAJOR.MINOR.PATCH[-PRERELEASE][+BUILD]
```

- `MAJOR.MINOR.PATCH`: non-negative integers, no leading zeroes (`1.9.0`).
- Pre-release after `-` (`1.0.0-alpha.1`); build metadata after `+` (`1.0.0+20130313144700`).
- Pre-release/build identifiers: `[0-9A-Za-z-]`, dot-separated, non-empty, no leading zeroes on numeric pre-release identifiers.

## Deciding the bump

First match wins:

1. Backward-incompatible public-API change → **MAJOR** (reset MINOR, PATCH to 0).
2. New backward-compatible functionality, or a deprecation → **MINOR** (reset PATCH to 0).
3. Only backward-compatible bug fixes → **PATCH**.

Mixed set: highest wins (MAJOR > MINOR > PATCH) — one breaking change makes the release MAJOR.

**`0.y.z` (initial development):** anything may change; by convention still MINOR for features and PATCH for fixes, but flag that it carries no compatibility guarantee. Release `1.0.0` when the API is stable and in production.

## Validating a version string

Official regex:

```
^(0|[1-9]\d*)\.(0|[1-9]\d*)\.(0|[1-9]\d*)(?:-((?:0|[1-9]\d*|\d*[a-zA-Z-][0-9a-zA-Z-]*)(?:\.(?:0|[1-9]\d*|\d*[a-zA-Z-][0-9a-zA-Z-]*))*))?(?:\+([0-9a-zA-Z-]+(?:\.[0-9a-zA-Z-]+)*))?$
```

Groups: 1=major, 2=minor, 3=patch, 4=pre-release, 5=build. Flag: leading zeroes (`01.2.3`); a leading `v` (`v1.2.3` — a tag prefix, strip before validating); fewer than three components (`1.2`); an empty pre-release/build identifier (`1.0.0-`).

## Comparing precedence

1. Compare MAJOR, then MINOR, then PATCH numerically; first difference decides.
2. Equal core: a version **with** a pre-release is lower than one without (`1.0.0-alpha` < `1.0.0`).
3. Both pre-release: compare identifiers left to right — both numeric → numerically; else ASCII; numeric < alphanumeric; if all shared are equal, more identifiers wins.
4. Build metadata never affects precedence.

Low to high: `1.0.0-alpha < 1.0.0-alpha.1 < 1.0.0-alpha.beta < 1.0.0-beta < 1.0.0-beta.2 < 1.0.0-beta.11 < 1.0.0-rc.1 < 1.0.0`

## Edge cases

- **Breaking change shipped as minor/patch:** don't mutate the published version — release a compatibility-restoring patch, then break properly in a MAJOR.
- **Dependency bump only:** PATCH if fixing a bug, MINOR if it enables new functionality you expose.
- **Deprecating:** bump MINOR, document it, keep it for at least one more minor release before removing it in a MAJOR.
- **Released versions are immutable** — corrections ship as a new version.

## Compact output for another workflow

```
current: 1.4.2
bump: minor
next: 1.5.0
reason: added a backward-compatible --dry-run flag; no breaking changes
```

Add a `warning:` line only if the input version was invalid or you're under `0.y.z`. For anything not covered here (BNF grammar, full FAQ), read [semver.org](https://semver.org/).
