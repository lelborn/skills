---
name: conventional-commits
description: Write git commit messages that follow the Conventional Commits v1.0.0 spec. Use this whenever you are about to author a commit on the user's behalf, prepare or run `git commit`, suggest or clean up a commit message, split a diff into commits, write a PR title/description that references commits, or generate a CHANGELOG from history — even when the user never says "conventional commits". If a commit message is being written in a git repo, this skill applies.
---

# Conventional Commits

Format commit messages to carry machine-readable meaning, per [Conventional Commits v1.0.0](https://www.conventionalcommits.org/en/v1.0.0/). A well-formed history lets tools bump [SemVer](https://semver.org/) versions and generate changelogs, and lets a reader scan `git log` without opening diffs — but only if every commit obeys the format, so apply it by default rather than waiting to be asked.

## Format

```text
<type>[optional scope][!]: <description>

[optional body]

[optional footer(s)]
```

Only the header is required; `docs: fix typo in readme` is a complete, valid commit.

## Type — from the diff, not the request

Pick the type from what the change _does_, not what the user _called_ it. "Clean up the login page" could be a `fix`, `refactor`, or `style`; look at `git diff --staged` before deciding.

| Type       | For                                    | SemVer |
| ---------- | -------------------------------------- | ------ |
| `feat`     | A new feature                          | MINOR  |
| `fix`      | A bug fix                              | PATCH  |
| `docs`     | Documentation only                     | —      |
| `style`    | Formatting, no change in code meaning  | —      |
| `refactor` | Neither fixes a bug nor adds a feature | —      |
| `perf`     | Performance improvement                | —      |
| `test`     | Adding or fixing tests                 | —      |
| `build`    | Build system, tooling, dependencies    | —      |
| `ci`       | CI config and scripts                  | —      |
| `chore`    | Maintenance, not touching src or tests | —      |
| `revert`   | Reverts a previous commit              | —      |

`feat`/`fix` are about user-visible behaviour; `refactor` is internal-only. If a diff spans several types, that's a signal to split it into one commit per logical change rather than forcing one type over everything. For a revert, use `revert` with a footer naming the reverted SHA (`Refs: 676104e`).

## Fields

- **Scope** — optional noun in parentheses for the area affected (`fix(auth):`). Add it when the change is localised to one module/component; keep scopes consistent with existing history.
- **Description** — imperative mood ("add", not "added"/"adds"), ≤ ~72 chars, lowercase, no trailing period, and specific: `fix: handle empty array in parseArgs` beats `fix: bug`.
- **Body** — optional, one blank line after the description. Explain the _why_; don't narrate the diff line by line. Include only when the reason isn't obvious.
- **Footers** — optional, one blank line after the body. `Token: value` or `Token #123`; the token uses `-` for spaces (`Reviewed-by`, `Co-authored-by`). Add only when you have the information — don't invent issue numbers or reviewers.

## Breaking changes

A breaking change forces consumers to change their code — a removed/renamed API, a changed default, an altered response shape. Flag it with either or both:

- `!` before the colon: `feat(api)!: drop support for legacy tokens`
- a footer: `BREAKING CHANGE: <what breaks and how to migrate>`

It's a SemVer **MAJOR** regardless of type (`fix!` still breaks). Prefer including the footer when migration isn't obvious. Everything is case-insensitive **except** `BREAKING CHANGE` (synonym `BREAKING-CHANGE`), which must be uppercase.

## Examples

```text
feat(lang): add Polish translation
```

```text
fix: prevent racing of requests

Introduce a request id and dismiss responses from any but the latest request.

Refs: #123
```

```text
feat(api)!: send an email when a product ships

BREAKING CHANGE: the shipment webhook no longer includes the deprecated
`email_sent` flag; read shipment state from `status` instead.
```

## Reviewing a message

Flag: a missing type or `:` after it; an empty description or one not right after the prefix; a body/footer not separated by a blank line; a footer token with a space (except `BREAKING CHANGE`); `BREAKING CHANGE` not uppercase; a breaking diff with no marker; a type that doesn't match the diff.

## CHANGELOG from history

Group by type (`feat` → Features, `fix` → Fixes…), list anything with a breaking marker under **BREAKING CHANGES** regardless of type, and omit types with no user-facing meaning (`chore`, `style`, `ci`…) unless the user wants the full history. The companion `keep-a-changelog` skill covers this in depth.
