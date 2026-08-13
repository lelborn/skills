---
name: sc-init
description: Initialises a NetSuite SuiteCloud (SDF) Account Customization project — installs agent skills, sets up Prettier and ESLint, and writes package.json scripts, .gitignore, README, docs and CHANGELOG. Use this whenever the user is setting up, scaffolding, configuring, or "getting started with" a new or empty SuiteCloud/SDF/NetSuite project, or mentions adding linting, formatting, agent skills, or project docs to one — even if they only ask for one piece of it, and even if they don't say "init".
---

# SuiteCloud project init

Takes an empty (or partly set up) SuiteCloud Account Customization project and brings it to a consistent
baseline: agent skills installed, Prettier and ESLint configured, project docs written.

Everything needed is in this file — there are no bundled asset files to look for. File contents given in
fenced blocks below are written verbatim.

Work in the order below. Each stage depends on the previous one — the formatter needs its config before
`npm run format` means anything.

## 0. Gather inputs first

Most inputs are derivable. Only ask about what genuinely can't be worked out, and ask in one go rather than
drip-feeding questions.

Derive, don't ask:

- **Project name** — read `projectname` from `src/manifest.xml`. Say what was found and carry on. Only ask
  if the file is missing, or if the value is an obvious leftover default (`test-project`, `suitecloud-project`)
  that the user probably meant to change.
- **Skills to install** — always the twelve listed in stage 1.
- **Versions** — always latest; never carry a pinned version over from a previous run of this skill.

Ask about:

- **What the project does** — one sentence for the README. If the user doesn't know yet, write the
  placeholder text and move on rather than blocking on it.

Existing files listed here are **overwritten** with the exact contents given. `package.json` is the
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

**`.prettierignore`**

```
# Dependencies & build output
node_modules
build

# Lockfiles
package-lock.json
skills-lock.json

# Vendored skills & local tooling state
.agents
.claude
```

**`.prettierrc`**

```json
{
	"tabWidth": 4,
	"trailingComma": "es5",
	"singleQuote": true,
	"useTabs": true,
	"printWidth": 150,
	"plugins": ["@prettier/plugin-xml"],
	"overrides": [
		{
			"files": ["*.xml"],
			"options": {
				"useTabs": false,
				"tabWidth": 4,
				"printWidth": 150,
				"xmlWhitespaceSensitivity": "ignore",
				"xmlQuoteAttributes": "double"
			}
		}
	]
}
```

**`eslint.config.js`**

`package.json` has no `"type": "module"`, so a CommonJS config is valid:

```js
const js = require('@eslint/js');
const globals = require('globals');
const prettier = require('eslint-config-prettier');

module.exports = [
	// Ignore generated / non-source paths
	{
		ignores: ['node_modules/**', 'build/**', 'src/Objects/**', 'src/AccountConfiguration/**', 'src/Translations/**'],
	},

	// Base recommended rules
	js.configs.recommended,

	// SuiteScript source files (SuiteScript 2.x)
	{
		files: ['src/**/*.js'],
		languageOptions: {
			ecmaVersion: 'latest',
			sourceType: 'module',
			globals: {
				...globals.browser,
				// SuiteScript AMD + global APIs
				define: 'readonly',
				require: 'readonly',
				log: 'readonly',
			},
		},
	},

	// Jest unit tests
	{
		files: ['__tests__/**/*.js'],
		languageOptions: {
			ecmaVersion: 'latest',
			sourceType: 'module',
			globals: {
				...globals.jest,
				...globals.node,
			},
		},
	},

	// CommonJS config files run by Node
	{
		files: ['*.config.js', 'eslint.config.js'],
		languageOptions: {
			ecmaVersion: 'latest',
			sourceType: 'commonjs',
			globals: {
				...globals.node,
			},
		},
	},

	// Turn off rules that conflict with Prettier — keep last
	prettier,
];
```

If `require('eslint-config-prettier')` fails because the package has gone ESM-only, change **only** the
import style — keep the same object layout, the same `ignores`, `files` globs, `globals`, and rule order,
with `prettier` last so it can turn off conflicting rules.

## 3. package.json, .gitignore, README, docs

In `package.json`, replace `scripts` exactly as below. Keep the existing Jest and
`@oracle/suitecloud-unit-testing` dependencies, plus the Prettier/ESLint packages just installed — don't
rewrite `devDependencies` wholesale.

```json
{
	"scripts": {
		"test": "jest",
		"format": "prettier --write .",
		"check": "eslint . && prettier --check . && jest"
	}
}
```

**`.gitignore`**

Note it deliberately does **not** ignore `.agents`, `.claude` or `skills-lock.json` — those are committed,
and are only excluded from Prettier.

```
# IDEs and editors
.idea
*.iml
*.iws
.project
.classpath
*.launch
.settings
*.sublime-workspace
.vscode

# Dependency directories
node_modules

# TypeScript cache
*.tsbuildinfo

# Logs
logs
.logs
*.log
npm-debug.log*

# Packaged SuiteCloud projects
build

# misc
.sass-cache

# Optional npm cache directory
.npm

# System Files
.DS_Store
Thumbs.db

# Output of 'npm pack'
*.tgz

# Project config
project.json
```

**`README.md`** — substitute the project name and the one-line description (or keep the placeholder):

```markdown
# <project-name>

A NetSuite account customization built with the [SuiteCloud Development Framework (SDF)](https://docs.oracle.com/en/cloud/saas/netsuite/ns-online-help/chapter_4702629769.html). [Placeholder: describe what this project does.]

See [Documentation](docs/README.md).

## Versioning

Releases follow [Semantic Versioning](https://semver.org/) and changes are logged in [CHANGELOG.md](CHANGELOG.md).
```

**`docs/README.md`**

```markdown
# <project-name> — Documentation

[Placeholder: describe what this customization does and how it fits into the account.]

## Structure

- `src/` — SDF project source (objects, file cabinet, manifest and deploy descriptors)
- `__tests__/` — Jest unit tests

## Local development

    npm install       # install dependencies
    npm run format    # format everything with Prettier
    npm run check     # lint, check formatting, and run tests

## Deployment

[Placeholder: describe the SDF deployment process and target accounts.]
```

**`CHANGELOG.md`** — substitute today's date:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.0] - <YYYY-MM-DD>

### Added

- Initial project setup.
```

Create the linked files even when their content is only a placeholder — a README pointing at a missing
`docs/README.md` is worse than a thin one.

Finish by running:

```bash
npm run format
npm run check
```

Expect the XML in `src/manifest.xml` and `src/deploy.xml` to reformat on the first pass. Fix any failures
before finishing.

Report what was done, along with anything that needed a judgement call — a skill that had to be symlinked
by hand, a lint rule that was failing, a README section left as placeholder.
