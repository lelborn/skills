# Docs templates

Generic skeletons for the `.docs/` tree. Placeholder tokens: `{PROJECT}`, `{KIND}` (a unit-kind folder — `lib`, `modules`, `objects`, …), `{UNIT}` (human title), `{UNIT_ID}` (code identifier), `{SOURCE_PATH}` (repo-relative path to the source). Adapt headings to the project; drop any section that doesn't apply. Style throughout: present tense, precise, tables for identifiers/params, short paragraphs, no filler.

## Index — `.docs/README.md`

One table per category: a **Project** table for the ops docs, then one table per unit kind. Add a row whenever a doc is created.

```markdown
# {PROJECT} documentation

<!-- One or two sentences: what this project is, and that this page is the map. -->

For a project overview, see the [root README](../README.md).

## Project

| Doc                                 | Covers                              |
| ----------------------------------- | ----------------------------------- |
| [Project layout](project-layout.md) | Repo tree, conventions, commands    |
| [Configuration](configuration.md)   | Settings and secrets (by reference) |

## {KIND}

<!-- One table per unit kind — e.g. Libraries, Modules, Services, Objects, Scripts. -->

| Doc                           | Source      |
| ----------------------------- | ----------- |
| [{UNIT}]({KIND}/{UNIT_ID}.md) | `{UNIT_ID}` |

## Related

- [CHANGELOG](../CHANGELOG.md)
```

Every category folder that exists (`lib/`, `objects/`, …) has a matching table here.

## Unit doc — `.docs/{KIND}/{UNIT_ID}.md`

Filename is kebab-case, mirroring the source id with `_`→`-`. H1 is the human title.

```markdown
# {UNIT}

<!-- One line: what this unit is responsible for. -->

**Source:** [`{SOURCE_PATH}`](../../{SOURCE_PATH})

## IDs

<!-- Only if the unit has identifiers worth tabulating (record/field/script ids, UI paths). Drop otherwise. -->

|     |             |
| --- | ----------- |
| Id  | `{UNIT_ID}` |

## What it does

- <!-- Behaviour, notable edge cases, failure modes. -->

## Interface

<!-- Public surface: exports / API / params / config. Drop if trivial. -->

| Name | Type | Purpose |
| ---- | ---- | ------- |

## Related

- [{Related unit}](../{KIND}/other-id.md) — <!-- how they connect -->
```

For a small unit, H1 + purpose line + **Source** + a short **What it does** is a complete doc. Don't pad it.

## Project layout — `.docs/project-layout.md`

```markdown
# Project layout

<!-- 1–2 paragraphs: the shape of the repo and the main flows. -->

## Tree

<!-- ASCII tree of the significant directories, with a one-line note each. -->

## Conventions

<!-- Naming, module boundaries, where things live. -->

## Commands

| Task          | Command               |
| ------------- | --------------------- |
| Build         | <!-- real command --> |
| Test          | <!-- real command --> |
| Lint / format | <!-- real command --> |
```

## Configuration / secrets — `.docs/configuration.md`

```markdown
# Configuration

<!-- What is configurable, and where. Distinguish shipped defaults from runtime-required values. -->

## Settings

| Key | Default | Required | Notes |
| --- | ------- | -------- | ----- |

## Secrets

<!-- Never commit real values. Reference by name only; this is the one canonical home for secret setup. -->

- `{SECRET_NAME}` — set in <!-- where -->; used by <!-- what -->. Configure after deploy; never in git.
```

## Placeholder discipline

- Secrets/tokens: reference by name (`${{ secrets.NAME }}`, `custsecret_{prefix}_token`), never a value.
- Real internal IDs, partner/customer names, staff emails: replace with placeholders (`ACCOUNT-A`, `partner@example.com`) and note "configure in the target environment".
