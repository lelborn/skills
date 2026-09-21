---
name: docs-init
description: Scaffold a project's `.docs/` tree — an index (`.docs/README.md`) with linking tables, ops docs flat at the root, and one subfolder per kind of unit — establishing a house convention that `doc-it` and `doc-check` then maintain. Sizes the structure to the project and adapts to any language/stack; reuses an existing `.docs/` rather than clobbering it. Use when the user runs /docs-init or asks to set up documentation, create a docs folder or docs site structure, bootstrap project docs, or "get docs started". Not for writing a single doc (that's `doc-it`) or auditing docs (that's `doc-check`).
argument-hint: Optional — a docs area to focus (e.g. a unit kind, project-layout)
---

# docs-init

Lay down the skeleton of a project's documentation and the convention the rest of the docs will follow. The goal isn't to write every page — it's to establish a **navigable structure** (an index that links everything, one home per unit, source links, cross-links) so that `doc-it` can fill it in and `doc-check` can police it. The canonical templates are in [references/templates.md](references/templates.md).

Docs live under **`.docs/`** (a dotfolder, alongside `.github/`, `.agents/`, and friends), not `docs/`.

## The shape

```
.docs/
├── README.md            # the index — one linking table per category
├── project-layout.md    # repo tree, naming conventions, dev/build/test commands
├── <ops-doc>.md         # only the ops docs that apply, flat at the root (e.g. configuration.md)
├── <kind>/              # one subfolder per KIND of unit, named for that kind…
│   └── <unit-id>.md     #   …one kebab-case file per unit
└── <kind>/
    └── <unit-id>.md
```

Unit kinds are whatever the code is made of — `lib/`, `modules/`, `services/`, `components/`, or (for SDF) `objects/` + `scripts/` + `lib/`. Name the folders after the real kinds; don't force a generic `reference/`.

## The convention this establishes

- **`.docs/README.md` is the index** — a short intro, a one-hop pointer from the root `README.md`, and **one linking table per category**: a Project table for the ops docs, then a table per unit kind mapping each doc to its source identifier.
- **Ops docs sit flat at the `.docs/` root** (`project-layout.md` always; others only when they apply), named for what they cover.
- **One doc per unit**, in the subfolder for its kind. **Filename is kebab-case, mirroring the source id with `_`→`-`** (`ni_http.js` → `lib/ni-http.md`; `customrecord_ni_network_mapping.xml` → `objects/customrecord-ni-network-mapping.md`). H1 is a human-readable title; identifiers go in an early line or table, not the filename's stand-in.
- **Every unit doc links to its canonical source** (relative path). Code is the source of truth.
- **Cross-links are relative and bidirectional** — a "Related" section that points both ways.
- **One canonical home per fact.** Everything else summarises and links to it, rather than duplicating.
- **Hygiene:** never commit secrets, tokens, real account/internal IDs, or personal data (names, emails). Use placeholders and "configure after deploy" notes; distinguish shipped defaults from runtime-required config.

## Workflow

1. **Survey the repo.** Read the root `README.md`, any existing `.docs/`, and how the code is organised. Work out the **kinds of unit** to document and roughly how big the doc surface is.
2. **Propose the structure before writing it.** Show the tree and let the user adjust. Size it to the project:
    - **Small** → `.docs/README.md` plus `project-layout.md` and a flat doc or two.
    - **Larger** → a subfolder per unit kind, plus the ops docs that apply.
    - Don't scaffold folders the project has no use for.
3. **Write the index** (`.docs/README.md`) from the template: intro, pointer to the root README, a Project table, and one table per unit-kind category. Seed the tables with units you discovered; leave a clear note where a category is empty rather than inventing rows.
4. **Seed the ops docs** the project needs — `project-layout.md` first (real tree, real commands where obvious; `<!-- TODO -->` where not), plus others that apply. Never fabricate content.
5. **Wire the root README** to `.docs/README.md` with a single "Documentation" link if one isn't already there. Ask before restructuring an existing README.
6. **Verify:** relative links resolve, the index has a table per category, and nothing committed contains a secret or real ID.

## Reuse, don't clobber

If a `.docs/` tree already exists, treat it as the convention to extend, not replace: fill gaps (a missing index, an un-wired root README, a category with no table) and **ask before changing anything already written.**

## Boundaries

- **Filling in a specific page** is `doc-it`; **auditing the tree** is `doc-check`. This skill sets up the frame.
- In-code comments are a different concern (`comment-code`), not the `.docs/` tree.
