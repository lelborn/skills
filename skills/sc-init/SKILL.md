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

Most inputs are derivable. Ask everything you need in **one** message rather than blocking twice.

Derive, don't ask:

- **Project name** — read `projectname` from `src/manifest.xml`. Say what was found and carry on. Only query
  it if the file is missing, or if the value is a stock default (`test-project`, `suitecloud-project`) — and
  when querying, say it may well be deliberate rather than assuming it's wrong.
- **Skills to install** — always the twelve listed in stage 1.
- **Versions** — always latest; never carry a pinned version over from a previous run of this skill, and
  never copy a literal version number out of a docs page.

Ask about:

- **What the project does** — one sentence for the README. Ask once, alongside the project name if you're
  querying that too. If the user doesn't answer or doesn't know yet, write the placeholder and move on.

Existing files listed here are **overwritten** with the exact contents given. `package.json` is the
exception — merge into it as described in stage 3. Before overwriting anything that looks hand-edited
(a README with real content, a non-trivial `eslint.config.js`), say what will be replaced and check.

## 1. Agent skills

### List before installing

The CLI **silently drops** requested skill names that don't exist — it selects the ones it recognises,
reports success, and never mentions the missing one. So list each source first and confirm every name is
really there:

```bash
npx skills add lelborn/skills -l -y
npx skills add oracle/netsuite-suitecloud-sdk -l -y
```

Compare the output against the twelve names below. If a name is missing, **stop and report it** rather than
installing a partial set — and don't substitute a similar-looking name on your own initiative. Repos rename
skills; the fix belongs in this file, not in a one-off judgement call.

### Install

Create `.claude` first, in case the CLI needs it:

```bash
mkdir -p .claude
```

Use `-a claude-code`. (`-a claude` is rejected as an invalid agent name — don't waste a run on it.) Pass
`-y` so it doesn't prompt, and don't pass `--copy`. Run the two batches separately, in this order.

Batch 1:

```bash
npx skills add lelborn/skills \
  -s conventional-commits \
  -s keep-a-changelog \
  -s keep-it-human \
  -s semver \
  -s review-file \
  -a claude-code -y
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
  -a claude-code -y
```

The Oracle repo carries three more skills (`netsuite-ai-connector-instructions`, `netsuite-finance-analyst`,
`netsuite-sdf-project-documentation`). Leaving them out is deliberate, not an oversight.

### Fix the layout

Under some agent CLIs (cursor-cli, at least as of `skills@1.5.22`) the installer **copies rather than
symlinks even without `--copy`**, and its Installation Summary prints `.agents/skills/<name>` paths that
were never created — the real files land in `.claude/skills/<name>`. Don't trust the summary; check the
disk. Then normalise the layout so `.agents/skills/` holds the canonical copy and `.claude/skills/` holds
symlinks:

```bash
mkdir -p .agents/skills
for d in .claude/skills/*; do
  name=$(basename "$d")
  if [ -L "$d" ]; then
    echo "skip symlink: $name"
    continue
  fi
  if [ -d "$d" ]; then
    if [ -e ".agents/skills/$name" ]; then
      echo "conflict: .agents/skills/$name already exists, skipping $name"
      continue
    fi
    mv "$d" ".agents/skills/$name"
    ln -s "../../.agents/skills/$name" "$d"
    echo "moved+linked: $name"
  fi
done
```

Then verify: every named skill exists under `.agents/skills/`, every `.claude/skills/<name>` is a symlink to
`../../.agents/skills/<name>`, and `skills-lock.json` lists all twelve. Fix any stragglers by hand rather
than re-running a whole batch.

Note that `ls` run in parallel with an install returns stale results — verify only after the install
command has exited.

## 2. Prettier and ESLint

Prettier's docs recommend pinning the exact version locally. Resolve the latest version yourself via
`prettier@latest` — the version literal printed in the docs is whatever was current when the page was
written, and is not the instruction:

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

`npm warn Unknown env config "devdir"` appears on every npm and npx call in this environment. It is noise;
don't chase it.

**`.prettierignore`**

```ignore
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

`eslint-config-prettier` is CommonJS today, so `require` works — no need to check. If a future version goes
ESM-only and `require` fails, change **only** the import style: keep the same object layout, `ignores`,
`files` globs, `globals`, and rule order, with `prettier` last so it can turn off conflicting rules.

## 3. package.json, .gitignore, README, docs

In `package.json`, replace `scripts` exactly as below. Keep the existing Jest and
`@oracle/suitecloud-unit-testing` dependencies, plus the Prettier/ESLint packages just installed — don't
rewrite `devDependencies` wholesale. Leave the `"name"` field alone.

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

```ignore
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

Soft line breaks inside those markdown paragraphs are cosmetic — Prettier settles them either way. Don't
re-read the file to diff whitespace against this one.

Create the linked files even when their content is only a placeholder — a README pointing at a missing
`docs/README.md` is worse than a thin one.

Finish by running:

```bash
npm run format
npm run check
```

Prettier reformats whatever doesn't already match — typically `src/manifest.xml`, plus any of
`jest.config.js`, `jsconfig.json`, `package.json`, `suitecloud.config.js` and the sample test. Files left
unchanged (often `src/deploy.xml`) are fine, not a sign something was skipped. Fix any `check` failures
before finishing.

Report what was done, along with anything that needed a judgement call — a skill that had to be symlinked
by hand, a lint rule that was failing, a README section left as placeholder.
