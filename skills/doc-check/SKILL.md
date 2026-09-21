---
name: doc-check
description: Audit a project's `.docs/` tree and report what's wrong — committed secrets or real IDs, docs that have drifted from the code, broken relative and source links, units with no doc (and docs with no index row), missing cross-links, and structural or style lapses. Produces a severity-ranked report and changes nothing until asked. Use when the user runs /doc-check, asks to review/audit/check the docs, look for stale or broken documentation, or check docs for secrets/hygiene before publishing. For writing or fixing a page use `doc-it`; for standing up the tree use `docs-init`.
argument-hint: Optional — a docs path or area to focus the audit
---

# doc-check

Police the `.docs/` tree: find where it lies about the code, leaks something it shouldn't, or has rotted, and report it plainly. This skill **reads and reports** — it doesn't edit until the user asks. When they do want fixes, hand each one to `doc-it` (drift, missing pages) or apply the specific correction.

## What to check, worst first

Rank findings by severity. In order:

1. **Hygiene (critical).** Any committed secret, token, API key, real account/internal ID, or personal data (names, emails) in a doc. Quote the location and suggest the placeholder to use instead. This is the highest-priority class — a leak is worse than a gap.
2. **Drift / staleness.** Docs that describe code that has changed or no longer exists: source links pointing at moved or deleted files; described exports/params/behaviour that don't match the current source; stubs or incomplete work documented as if finished.
3. **Broken links.** Relative links between docs, source links, and index rows that resolve to nothing.
4. **Coverage gaps.** Units in the code with no doc; docs with no row in the `.docs/README.md` index; Related sections missing a back-link the other page has.
5. **Structure.** Index tables present and complete per category; every unit doc has a source link and a Related section; no fact duplicated verbatim across pages when it should have one canonical home (flag the copies, name the home).
6. **Style (minor).** First-person narration, filler, or prose where a table of IDs/params belongs; H1s that are bare identifiers instead of human titles.

## How to work

- **Establish the truth from code**, then hold docs against it — the source tree is authoritative, not the docs.
- **Resolve links for real** (check the target path exists), don't eyeball them.
- Scope to the path/area the user named if they gave one; otherwise sweep the whole `.docs/` tree.

## The report

Produce a **severity-ranked list**: each finding names the file (and line where relevant), states the problem in one line, and gives the concrete fix. Group by the classes above. End with a short summary — counts per severity and the few things worth doing first.

Then stop. Offer to apply fixes: route drift and missing pages to `doc-it`, redact any hygiene finding immediately on request (that one is worth flagging as urgent), and repair broken links in place. **Don't invent content to fill a gap** — a missing doc is reported and handed to `doc-it`, not fabricated here.
