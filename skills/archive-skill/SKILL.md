---
name: archive-skill
description: Retire a skill from this book without deleting it — move its folder out of `skills/` into a tracked `.archive/` folder (created if missing) so the skills CLI stops serving it, delist it from the README, and log the change. Use when the user runs /archive-skill, or asks to archive, retire, shelve, mothball, or "stop publishing" one of this repo's own skills. Not deletion — the skill stays in the repo, restorable.
argument-hint: Which skill to archive (its folder name under skills/)
disable-model-invocation: true
---

# Archive Skill

Retire a skill from the active book without losing it. Archiving moves a skill's source out of `skills/` — the folder the [`skills`](https://skills.sh) CLI publishes from — into a tracked `.archive/` folder at the repo root, so the skill stops being served and stops advertising itself, but stays in the repo and in git history, ready to restore. It is deliberate and destructive-adjacent (it changes what people install), so it runs only when asked — never on the model's own initiative.

This skill acts on **this repo's own skills** (the folders under `skills/`), not skills installed from elsewhere.

## Before moving anything

- **Resolve the target.** The argument is a skill's folder name. Confirm `skills/<name>/SKILL.md` exists. If the argument is missing or ambiguous, list the folders under `skills/` and ask which one — don't guess.
- **Refuse the impossible.** If there's no such folder, say so and stop. Don't archive `archive-skill` itself unless the user is explicit that they mean it.
- Everything below is reversible via git, so once the target is confirmed, do the whole workflow and report — no separate are-you-sure step.

## Steps

1. **Create `.archive/` at the repo root** if it doesn't exist yet (sibling to `skills/`, `.agents/`, `.claude/`). It is a normal tracked folder — do **not** add it to `.gitignore`; keeping archived skills in the repo is the whole point.
2. **Move the folder whole:** `skills/<name>/` → `.archive/<name>/`, contents intact (`SKILL.md` and anything alongside it). Prefer `git mv` so history follows the file; fall back to a plain move only if the path isn't tracked.
3. **Delist from the README.** Remove the skill's bullet from the "What's here" list in `README.md`. Leave every other bullet and the rest of the file untouched.
4. **Log the change.** Add one bullet under `## [Unreleased]` → `### Removed` in `CHANGELOG.md` — something like: **&lt;name&gt;** — archived to `.archive/`; no longer served by the skills CLI. Create the `### Removed` heading if it's missing, keeping the six-type order. Defer to the `keep-a-changelog` skill for placement and wording; no version bump or date here — that happens when a release is cut.
5. **Flag the dogfooded installs.** If `<name>` also appears in `skills-lock.json`, `.agents/skills/`, or `.claude/skills/`, note that those are local CLI installs of this book (gitignored) and the user may want to re-run the skills CLI to drop it. **Don't edit those files** — they're managed by the CLI, not by hand.

## Report

Finish with a short summary: what moved and to where, the exact changelog line added, whether the README bullet was removed, and any dogfooded-install note from step 5. One line on how to undo it (see below).

## Restoring an archived skill

Reverse the move: `git mv .archive/<name> skills/<name>`, add the README bullet back to "What's here", and log it under `### Added` (or `### Changed`) in the changelog. Then, if it was one of the dogfooded skills, the user can re-run the skills CLI to reinstall it.

## Guardrails

- **Never delete.** This skill only moves and records; the archived copy always survives.
- **One skill at a time, named explicitly.** Don't touch any skill other than the resolved target.
- **Don't cut a release.** Adding the changelog note is the end of the job — no tags, no version bump, no push unless the user separately asks (see `keep-a-changelog`).
