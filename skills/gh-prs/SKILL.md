---
name: gh-prs
description: The working standard for opening, writing, reviewing, and merging GitHub pull requests well — apply it whenever creating, describing, updating, reviewing, or merging a PR (via `gh`, the API, or the UI), writing a PR title or description, or getting a branch ready to open, even when not explicitly asked to "follow standards". Covers branching off main, one logical change per PR, Conventional-Commit titles (the squash commit is built from them), writing the body against the repo's template, a verifiable test plan, review etiquette, and never merging with unchecked tasks or without being asked.
---

# Pull requests

A pull request is where a change earns its way into the default branch. A good PR is small enough to review, titled so its history reads cleanly, described so a reviewer knows _why_ without reading every line, and honest about what's been verified. This standard applies to every PR you open, review, or merge — it isn't a step you wait to be asked for.

## Before you open

- **Never work on `main`.** Branch first; the PR merges a branch into the default branch, never the default branch into itself. Never push directly to `main`.
- **One logical change per PR.** A reviewer should be able to hold the whole thing in their head. If it does two unrelated things, it's two PRs. Big, tangled PRs get rubber-stamped or stalled — neither is review.
- **Tie it to an issue.** If an issue exists, the PR implements its spec and its `Done when`; reference it. If none does and the change is non-trivial, consider filing one first (see the `gh-issues` skill).
- **Open as a draft while it's in progress**, and mark it ready only when the test plan can actually pass. Don't request review on something you know is red.

## Title

- **It's a Conventional Commit.** On a squash-merge repo the title _becomes_ the commit message, so it must stand on its own: `feat: add profile export`, `fix: stop crash on empty display name`. Imperative, specific, no trailing period. (See the `conventional-commits` skill.)
- Match the type to the work, and to the issue it closes.

## Body — follow the template

If `.github/PULL_REQUEST_TEMPLATE.md` exists, fill it; where it doesn't, cover the same ground (the `gh-templates` skill sets one up):

- **Summary** — _why_ this PR exists, in one to three bullets. Link the issue with `Closes #123`. Don't narrate the diff; the diff is right there.
- **Changelog** — one user-facing line in a Keep a Changelog type (Added / Changed / Deprecated / Removed / Fixed / Security), ready to paste into `[Unreleased]`, or `none`. (See the `keep-a-changelog` skill.)
- **Docs** — one line: what was documented, or why nothing was needed.
- **Test plan** — see below.
- **Notes** — breaking changes, migrations, compatibility. Delete if empty.

Write it like a person, not a changelog bot (the `keep-it-human` skill applies).

## Test plan — the merge gate

The `- [ ]` items in the test plan are how everyone knows the change works. Treat them as the gate:

- Make each item **verifiable** — a specific command, check, or flow, not "tested it".
- **Tick a box only when you've truly verified it**: you ran it (or CI did) and it passed. Checking an unverified box is a violation.
- If an item no longer applies, edit it to `- [x]` with a `(obsolete: …)` reason rather than leaving a ghost todo.
- **Don't merge with any box — here or in the linked issue's `Done when` — still unchecked.** Finish it or mark it obsolete first; never merge and "file a follow-up" for the rest.

## Reviewing

- Review the change, not the person — be concrete, kind, and specific about what to change and why.
- Distinguish blocking problems from nits; say which is which.
- Resolve a thread when it's genuinely addressed; don't leave stale open threads that block others or hide unfinished discussion.

## Keeping the branch healthy

- Keep it current with the base branch so review reflects reality and required checks run on the real merge state.
- **Never `--force` / `--force-with-lease` to a shared branch, and never `--no-verify` or skip hooks** to get green. Fix the cause. Force-pushing over a reviewer's context without a heads-up is its own rudeness.

## Merging

- **Merge only when the user has explicitly asked**, and only after the test plan and any linked issue's `Done when` are all checked or honestly obsolete. Merging is an outward, hard-to-reverse action — a green CI is not the same as being told to merge.
- Respect the repo's ruleset (required checks, approvals, conversation resolution — the `gh-protect` skill sets these). Don't look for ways around a failing required check; make it pass.
- On squash-merge repos, confirm the squash title/body is the clean Conventional Commit you want in history.
- After merge, delete the branch; the linked issue closes itself via `Closes #`.

## Hard no

- Pushing to `main`, or merging the PR when the user didn't ask.
- `--force` / `--no-verify` / skipping hooks on a shared branch.
- Merging while any `- [ ]` remains in the PR or a linked issue.
- Bypassing a required status check or approval instead of satisfying it.

```
❌ "CI is green, I'll merge and open a follow-up for the untested flow."
✅ Ran the flow, checked the box; the user asked to merge; required checks pass → merge, delete branch.
```
