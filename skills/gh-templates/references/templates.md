# Template reference

Canonical bodies for the `.github/` contribution templates. Write these into the repo, then adapt the lines flagged with `ADAPT:` to the actual project. Keep the structure and the Conventional-Commit prefixes.

## `.github/ISSUE_TEMPLATE/bug.md`

```markdown
---
name: Bug
about: Something is broken or wrong
title: 'fix: '
labels:
    - bug
---

<!--
Fill every heading you can. Use n/a for anything that does not apply.
This body is the spec to implement against.
-->

## What happened

## What should happen

## How to reproduce

1.
2.
3.

## Where

<!-- ADAPT: name the kinds of locations that fit this project — component, file, screen, service, or error output. -->

## Evidence

<!-- Screenshot, log, stack trace. Delete this section if you have none. -->
```

## `.github/ISSUE_TEMPLATE/feature.md`

```markdown
---
name: Feature
about: New behaviour or capability
title: 'feat: '
labels:
    - enhancement
---

<!--
Fill every heading you can. Use n/a for anything that does not apply.
This body is the spec to implement against.
-->

## Problem

<!-- Who is stuck, and why. Not the solution yet. -->

## Proposal

<!-- What to build. ADAPT: name the component, module, or screen if you know it. -->

## Done when

- [ ]
- [ ]

## Out of scope

<!-- Delete this section if empty. -->
```

## `.github/ISSUE_TEMPLATE/chore.md`

```markdown
---
name: Chore
about: Maintenance, deps, CI, refactors, docs
title: 'chore: '
labels:
    - chore
---

<!--
Change the title prefix to docs, ci, refactor, test, or build when that fits better.
Fill every heading you can. Use n/a for anything that does not apply.
This body is the spec to implement against.
-->

## Why

## Work

## Done when

- [ ]
```

## `.github/ISSUE_TEMPLATE/config.yml`

`blank_issues_enabled: false` forces contributors through a template. Set it `true` to also allow free-form issues. `contact_links` is optional — use it to point elsewhere (discussions, security policy, support).

```yaml
blank_issues_enabled: false
# contact_links:
#   - name: Security report
#     url: https://github.com/OWNER/REPO/security/advisories/new
#     about: Report a vulnerability privately, not as a public issue.
```

## `.github/PULL_REQUEST_TEMPLATE.md`

The **Test plan** is the main thing to adapt: swap the checkboxes for the repo's real lint/test/build commands.

```markdown
<!--
PR title must be a Conventional Commit — the squash commit is built from it.
Write like a person, not a changelog bot. Skills: conventional-commits, keep-a-changelog, keep-it-human.
-->

## Summary

<!-- Why this PR exists. One to three bullets. Link issues with Closes #123. Do not narrate the diff. -->

## Changelog

<!-- One of: Added / Changed / Deprecated / Removed / Fixed / Security / none -->
<!-- One user-facing line, ready to paste into [Unreleased] later. -->

## Docs

<!-- One line: what was documented, or why no doc was needed. -->

## Test plan

<!-- ADAPT: replace with the repo's real commands. Leave this TODO if you can't determine them. -->

- [ ] Lint passes
- [ ] Tests pass
- [ ] Verified the change manually

## Notes

<!-- Breaking change, migration, compatibility. Delete this section if empty. -->
```

## `.github/dependabot.yml`

Dependabot config has its own skill — use **`gh-depbot`**, don't scaffold it from here.
