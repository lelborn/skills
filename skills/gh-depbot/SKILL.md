---
name: gh-depbot
description: Configure a repository's `.github/dependabot.yml` for Dependabot version updates — detect the package ecosystems actually present, then scaffold or update the config with sensible defaults (weekly schedule, `chore` commit prefix, grouped minor/patch updates to cut PR noise). Use when the user runs /gh-depbot or asks to set up, add, or fix Dependabot, dependabot.yml, automated dependency updates, or grouped/scheduled dependency PRs. Creates `.github/` if missing, never clobbers an existing config without asking, and keeps private-registry credentials out of the file.
argument-hint: Optional — an ecosystem to add (e.g. npm, github-actions)
---

# gh-depbot

Write a `.github/dependabot.yml` that keeps a repo's dependencies current without drowning it in pull requests. The full ecosystem table, option reference, and example configs are in [references/config.md](references/config.md) — consult it for exact keys and allowed values rather than guessing.

This configures **version updates** (the `updates:` blocks). Dependabot **security updates** and whether Dependabot runs at all are repository settings (Settings → Code security), not this file — mention that, but don't try to toggle them here.

## Workflow

1. **Detect the ecosystems.** Scan the repo for manifests and map each to a `package-ecosystem` value (the table in [references/config.md](references/config.md) has the mapping — `package.json`→`npm`, `go.mod`→`gomod`, `Cargo.toml`→`cargo`, `pyproject.toml`/`requirements*.txt`→`pip` or `uv`, `pom.xml`→`maven`, `Dockerfile`→`docker`, and always `github-actions` when `.github/workflows/` exists, etc.). Only include ecosystems that are actually present — never scaffold empty blocks.
2. **Handle monorepos.** When a manifest lives outside the root, set `directory` to its path; when several share an ecosystem, prefer `directories` (it supports `*` globs — `directory` does not).
3. **Reuse, don't clobber.** If `.github/dependabot.yml` already exists, read it first and **add/adjust** the relevant `updates` entries in place; ask before changing or removing anything already there.
4. **Apply the house defaults** per entry (let the user override):
    - `schedule: { interval: weekly }`
    - `commit-message: { prefix: chore, include: scope }` — matches the Conventional-Commit house style.
    - **Group minor & patch updates** so routine bumps land as one PR per ecosystem instead of a swarm; keep majors separate so they get individual review. (See the grouped example in [references/config.md](references/config.md).)
    - Consider `open-pull-requests-limit`, `labels`, `target-branch`, and `ignore`/`allow` only when the user wants them — don't clutter the file with defaults that already apply.
5. **Write** `.github/dependabot.yml` (create `.github/` if missing; it sits directly in `.github/`, not in a subfolder). Then note it takes effect once merged to the default branch — there's no apply command.

## Private registries

If a dependency comes from a private registry, add a top-level `registries:` block and reference it from the entry — but **never put tokens or passwords in the file** (it's committed and often public). Use `${{ secrets.NAME }}` referencing Dependabot secrets set in repo/org settings, and tell the user to create those secrets themselves.

## Guardrails

- **Only real ecosystems.** No speculative blocks for package managers the repo doesn't use.
- **`chore` prefix + grouped minor/patch** are the defaults that make this useful; keep them unless the user opts out.
- **No credentials in the file.** Registry auth is via Dependabot secrets, referenced by name.
- **Don't clobber.** Merge into an existing `dependabot.yml`; ask before altering existing entries.
- Security updates and enabling Dependabot are **repo settings**, out of scope for this file — point the user to them, don't attempt them.
