---
name: sc-init
description: Initializes a NetSuite SuiteCloud (SDF) Account Customization project — installs agent skills, sets up Prettier and ESLint, writes package.json scripts, .gitignore, README, docs and CHANGELOG, then runs git init. Use this whenever the user asks for sc-init or suitecloud init, or is setting up, scaffolding, configuring, or "getting started with" a new or empty SuiteCloud/SDF/NetSuite project, or mentions adding linting, formatting, agent skills, or docs to one — even if they only ask for one piece of it.
---

# SuiteCloud init

Takes an empty (or partly set up) SuiteCloud Account Customization project and brings it to a consistent
baseline: agent skills installed, Prettier and ESLint configured, project docs written, and git initialised.

Work in the order below. Each stage depends on the previous one — the formatter needs its config before
`npm run format` is meaningful, and git needs the files before the initial commit.

## 0. Gather inputs first

Most inputs are fixed or derivable. Only ask about what genuinely can't be worked out, and ask in one go
rather than drip-feeding questions.

Derive, don't ask:

- **Project name** — read `projectname` from `src/manifest.xml`. Say what was found and carry on; only ask
  if the file is missing or the value looks like a leftover default the user probably wants changed.
- **Skills to install** — always the twelve listed in stage 1.
- **Versions** — always latest; never carry a pinned version over from a previous run of this skill.

Ask about:

- **What the project does** — one sentence for the README. If the user doesn't know yet, write the
  placeholder text and move on rather than blocking on it.

Existing files listed in this skill are **overwritten** with the exact contents given. `package.json` is the
exception — merge into it as described in stage 3. Before overwriting anything that looks hand-edited
(a README with real content, a non-trivial `eslint.config.js`), say what will be replaced and check.

## 1. Agent skills

Create `.claude` **before** installing. Without it the CLI can silently skip creating the symlink, and the
skills then won't be visible to Claude:

```bash
mkdir -p .claude
```

Install with `-a claude` (if the CLI rejects that agent name, retry with `-a claude-code`), symlinked rather
than copied — so **do not** pass `--copy` — and `-y` so it doesn't prompt. Run the two batches separately,
in this order.

Batch 1:

```bash
npx skills add lelborn/skills \
  -s conventional-commits \
  -s keep-a-changelog \
  -s keep-it-human \
  -s semantic-versioning \
  -s review-file \
  -a claude -y
```

Batch 2:

```bash
npx skills add oracle/netsuite-suitecloud-sdk \
  -s netsuite-suitescript-records-reference \
  -s netsuite-sdf-roles-and-permissions \
  -s netsuite-uif-spa-reference \
  -s netsuite-owasp-secure-coding \
  -s netsuite-suitescript-upgrade \
  -s netsuite-sdf-safe-guide \
  -s netsuite-suitescript-learning \
  -a claude -y
```

Then verify, rather than trusting the CLI's exit code: each named skill exists under `.agents/skills/`,
each `.claude/skills/<name>` is a **symlink** to it, and `skills-lock.json` was written. If a symlink is
missing, create it manually rather than re-running the whole batch.

## 2. Prettier and ESLint

Read the current install docs before installing — flags and recommended setup do change. Prettier's docs
currently recommend pinning the exact version locally, so resolve the latest version and pin that:

```bash
npm install --save-dev --save-exact prettier@latest
npm install --save-dev @prettier/plugin-xml
```

The XML plugin matters here — SDF projects are full of XML (`src/manifest.xml`, `src/deploy.xml`, objects)
and it's what lets Prettier format them.

Then install ESLint and what the config below needs:

```bash
npm install --save-dev eslint @eslint/js globals eslint-config-prettier
```

Do **not** run the interactive `npm init @eslint/config` — it will produce a different config from the one
this project expects.

Write these three files exactly as given:

- `.prettierignore` → `assets/prettierignore`
- `.prettierrc` → `assets/prettierrc.json`
- `eslint.config.js` → `assets/eslint.config.js`

`package.json` has no `"type": "module"`, so a CommonJS `eslint.config.js` is valid. If
`require('eslint-config-prettier')` fails because the package has gone ESM-only, change **only** the import
style — keep the same object layout, the same `ignores`, `files` globs, `globals`, and rule order, with
`prettier` last so it can turn off conflicting rules.

## 3. package.json, .gitignore, README, docs

In `package.json`: replace `scripts` exactly as below. Keep the existing Jest
and `@oracle/suitecloud-unit-testing` dependencies, plus the Prettier/ESLint packages just installed —
don't rewrite `devDependencies` wholesale.

```json
{
	"scripts": {
		"test": "jest",
		"format": "prettier --write .",
		"check": "eslint . && prettier --check . && jest"
	}
}
```

Write `.gitignore` from `assets/gitignore`. Note it deliberately does **not** ignore `.agents`, `.claude` or
`skills-lock.json` — those are committed, and are only excluded from Prettier.

Write these from their templates, substituting the project name and the one-line description (or the
placeholder if the user didn't have one):

- `README.md` → `assets/README.template.md`
- `docs/README.md` → `assets/docs-README.md`
- `CHANGELOG.md` → `assets/CHANGELOG.md`

Create the linked files even when their content is only a placeholder — a README pointing at a missing
`docs/README.md` is worse than a thin one. Don't add a CI workflow unless asked.

Finish the stage by running:

```bash
npm run format
npm run check
```

Expect the XML in `src/manifest.xml` and `src/deploy.xml` to reformat on the first pass. Fix any failures
before moving on, so the first commit is clean.
