---
name: doc-it
description: Create or update the documentation page for a specific target — a module, package, service, feature, or subsystem — matching the repo's existing `.docs/` conventions, and keep the docs index in sync. Pulls facts from the actual source, links the doc to its canonical source file, cross-links related docs, and keeps secrets and real IDs out. Use when the user runs /doc-it, asks to document or write docs for a named thing, update a doc after a code change, or satisfy a PR's Docs line. For standing up a docs tree from scratch use `docs-init`; for auditing docs use `doc-check`.
argument-hint: What to document (a module, file, feature, or subsystem)
---

# doc-it

Write or update one documentation page so it tells the truth about the code, reads well, and stays wired into the rest of the docs. The unit of work is a single target and its page — plus the index row and cross-links that keep it findable.

## Match the house style — don't impose your own

Before drafting, learn how this repo documents things:

- Read `.docs/README.md` (the index) and **an existing sibling doc of the same kind**. Mirror its structure, depth, section names, and tone. The repo's own docs are the template.
- If there's no `.docs/` tree yet, say so and offer to run **`docs-init`** first; if the user just wants the one page, create a minimal `.docs/` and follow the anatomy below.

## Anatomy of a unit doc

When there's no sibling to copy, a good page has:

- **H1** — the human-readable name (identifiers go in a line or table below, not the title).
- **One-line purpose.**
- **Source link** — a relative link to the canonical source file(s) under the repo. Every unit doc has this; code is the source of truth.
- **What it does** — behaviour, notable edge cases, failure modes.
- **Interface** — a table of the public surface (exports / API / parameters / config) when it's non-trivial.
- **Related** — relative links to the docs it connects to, added **both ways** (update the other doc's Related too).

The page lives in the subfolder for its kind, with a **kebab-case filename mirroring the source id** (`_`→`-`): `ni_http.js` → `.docs/lib/ni-http.md`, `customrecord_ni_network_mapping.xml` → `.docs/objects/customrecord-ni-network-mapping.md`.

Present tense, precise, tables for identifiers and parameters, no filler or diff-narration (the `keep-it-human` skill applies).

## Workflow

1. **Resolve the target** and its canonical source path(s). If it's ambiguous, ask which module/feature.
2. **Read the source** — document what the code actually does, not what you assume. Pull real names, signatures, config keys, and IDs from it.
3. **Locate or place the doc** — find the existing page; if new, put it in the subfolder for its kind under `.docs/`, named per the rule above (create the kind's folder and its index table if this is the first of its kind).
4. **Draft or update** to match the sibling shape / the anatomy above.
5. **Sync the index** — add or update the row in the matching `.docs/README.md` table. Every new or renamed doc changes the index.
6. **Cross-link** — add the bidirectional Related links on the pages this one relates to.
7. **Verify** — the source link and every relative link resolve; no secret, token, or real internal ID/PII slipped in; one canonical home (summarise and link rather than duplicating another doc's content).

## When code changed or went away

- **Changed:** update the affected sections and the source link; don't leave a stale description standing.
- **Deleted:** don't document dead code — remove or retire the page and its index row, and fix any Related links that pointed at it.
- **Incomplete/stub:** mark it explicitly (a status note or table cell) rather than describing aspirational behaviour as real.

## Hygiene

Docs are often public. Never write real secrets, tokens, account/internal IDs, partner/customer names, or staff emails into a committed doc — use placeholders and "configure in the target environment", and keep verbatim secret/parameter help in its one canonical home (usually the configuration/secrets doc at the `.docs/` root), linked from elsewhere.
