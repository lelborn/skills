---
name: tooling-init
description: Sets up a JavaScript/TypeScript project's core dev tooling — Prettier (formatting), ESLint (linting) and Playwright (end-to-end tests) — by first evaluating the project (package manager, TS vs JS, module type, framework, existing config) and then installing and wiring each tool with ignore rules, editor/IDE settings and package.json scripts that chain sensibly against whatever is already there, finishing with a verification run. Use this whenever the user wants to add or set up formatting, linting or e2e testing on a JS/TS project, mentions Prettier, ESLint or Playwright, asks to "set up tooling", "add a linter/formatter", "configure format on save", or get a project's quality baseline in place — even if they only ask for one of the three, and even if they never name the tools.
---

# tooling-init

Brings a JavaScript or TypeScript project to a consistent tooling baseline: **Prettier** for
formatting, **ESLint** for linting, and **Playwright** for end-to-end tests — each installed, configured,
ignored, wired into the editor, and reachable through package.json scripts, with the whole chain verified
at the end.

This assumes a Node-based JS/TS project (Playwright here means browser/e2e testing). If you're pointed at a
Python or other non-JS project, stop and say so rather than forcing these tools on.

Two ideas run through the whole skill:

- **Evaluate before you touch anything.** Almost every command below changes depending on what the project
  already is — the package manager, TS vs JS, how modules resolve, which configs already exist. Detect
  first (stage 0), then act. This is what makes the skill safe to re-run: an existing, hand-edited config
  is reported and left alone, not clobbered.
- **Resolve versions yourself, always latest.** The official docs pin a literal version in their examples
  (e.g. `prettier@3.x.y`) — that's whatever was current when the page was written, not an instruction.
  Install `@latest` and let the exact number land in `package.json`. Never copy a version literal out of a
  docs page or a previous run.

Work through the stages in order — each depends on the last. `npm run format` means nothing before Prettier
has a config; the verify run means nothing before the scripts exist.

## 0. Evaluate the project

Look before you leap. Gather all of this up front and report what you found in one message, then proceed —
only ask the user when something is genuinely ambiguous (see the end of this stage).

- **package.json** — if missing, create one (`npm init -y`, or the package-manager equivalent). Both ESLint
  and Playwright need it to exist.
- **Package manager** — read the lockfile. This drives every install and init command from here on:

    | Lockfile                 | Manager | Install dev dep  | Create/init runner      |
    | ------------------------ | ------- | ---------------- | ----------------------- |
    | `package-lock.json`      | npm     | `npm install -D` | `npm init <pkg>@latest` |
    | `pnpm-lock.yaml`         | pnpm    | `pnpm add -D`    | `pnpm create <pkg>`     |
    | `yarn.lock`              | yarn    | `yarn add -D`    | `yarn create <pkg>`     |
    | `bun.lock` / `bun.lockb` | bun     | `bun add -d`     | `bun create <pkg>`      |

    No lockfile? Default to npm and say so.

- **TypeScript vs JavaScript** — `tsconfig.json` or any `*.ts`/`*.tsx` means TS. This changes the ESLint
  install and config, and Playwright's `--lang`.
- **Module type** — `"type": "module"` in package.json means ESM. It matters less than you'd think here,
  because the ESLint config below is always written as `eslint.config.mjs` (ESM), which ESLint loads
  regardless of the project's `type`. Noting it just avoids surprises elsewhere.
- **Framework** — scan dependencies for React, Vue, Svelte, Next, etc. Use it to add the matching editor
  language blocks (stage 5) and, if you set up Playwright's `webServer`/`baseURL`, to point at the right dev
  command. Don't invent framework-specific ESLint plugins the user didn't ask for; keep the base config lean.
- **Existing tool config** — check for `.prettierrc*`, `prettier.config.*`, `eslint.config.*`,
  `.eslintrc*`, `playwright.config.*`, and `.vscode/`. For any tool already configured, **report it and skip
  its config file** rather than overwriting — merge only the additive pieces (a missing script, a missing
  `.vscode` recommendation). Idempotency is the point: running this on a half-set-up project should finish
  the job, not undo it.

Ask the user only when detection is genuinely unresolved — e.g. a non-empty directory with no package.json
and no obvious entry point, or a legacy `.eslintrc` you'd be migrating off (flat config is the modern
default; migrating is a bigger decision than this skill should make silently).

## 1. Prettier

Install pinned to an exact version — formatting output can shift between releases, so a floating range would
let a teammate's `format` reflow files yours just touched. (`--save-exact` on npm/pnpm, `--exact` on
yarn/bun.)

```bash
npm install -D --save-exact prettier@latest
```

**`.prettierrc`** — start minimal. Prettier is opinionated by design; an empty object adopts its defaults,
which is the intended starting point. Teams tune from there.

```json
{}
```

**`.prettierignore`** — Prettier already skips `node_modules` and honours `.gitignore`; this covers the
build and test artefacts worth being explicit about.

```
# Build output
dist
build
coverage

# Playwright artefacts
playwright-report
test-results
blob-report
```

## 2. ESLint

Skip the interactive `npm init @eslint/config` — it asks a series of questions and produces a config that
varies run to run. Writing the config directly is deterministic and lets Prettier and ESLint coexist
cleanly. Install what the config needs:

- **JavaScript:** `eslint @eslint/js globals eslint-config-prettier`
- **TypeScript:** the above plus `typescript-eslint` (and `typescript` itself if it isn't already a dep)

```bash
npm install -D eslint @eslint/js globals eslint-config-prettier
```

Write **`eslint.config.mjs`**. The load-bearing detail is `eslint-config-prettier` **last** — it switches
off the stylistic rules that would otherwise fight Prettier, so it has to win. `globalIgnores` handles
directories (a plain `ignores` key only matches files); `node_modules` and `.git` are ignored by default.

**JavaScript:**

```js
import js from '@eslint/js';
import globals from 'globals';
import eslintConfigPrettier from 'eslint-config-prettier';
import { defineConfig, globalIgnores } from 'eslint/config';

export default defineConfig([
	globalIgnores(['dist/**', 'build/**', 'coverage/**', 'playwright-report/**', 'test-results/**']),
	{
		files: ['**/*.{js,mjs,cjs}'],
		plugins: { js },
		extends: ['js/recommended'],
	},
	{
		files: ['**/*.{js,mjs,cjs}'],
		languageOptions: { globals: { ...globals.browser, ...globals.node } },
	},
	// Keep last: turn off rules that conflict with Prettier.
	eslintConfigPrettier,
]);
```

**TypeScript:**

```js
import js from '@eslint/js';
import globals from 'globals';
import tseslint from 'typescript-eslint';
import eslintConfigPrettier from 'eslint-config-prettier';
import { defineConfig, globalIgnores } from 'eslint/config';

export default defineConfig([
	globalIgnores(['dist/**', 'build/**', 'coverage/**', 'playwright-report/**', 'test-results/**']),
	{
		files: ['**/*.{ts,tsx,js,mjs,cjs}'],
		extends: [js.configs.recommended, tseslint.configs.recommended],
		languageOptions: { globals: { ...globals.browser, ...globals.node } },
	},
	// Keep last: turn off rules that conflict with Prettier.
	eslintConfigPrettier,
]);
```

Drop `globals.browser` or `globals.node` if the project is clearly only one or the other; leaving both in is
a safe default for a project you don't yet know well.

## 3. Playwright

Use the official installer — it writes `playwright.config`, an example spec, and appends its own
`.gitignore` block (`/test-results/`, `/playwright-report/`, `/blob-report/`, `/playwright/.cache/`), which
is exactly the ignore-rule work you'd otherwise do by hand. Run it non-interactively so it doesn't block on
prompts; `--quiet` takes the defaults for anything you don't flag:

```bash
npm init playwright@latest -- --quiet --lang=ts
```

- `--lang=js` for a JavaScript project.
- It installs all three browser engines by default (a few hundred MB). Add `--browser=chromium` to trim that
  to one if the user wants a lighter setup; otherwise let the default stand — cross-browser coverage is the
  reason to use Playwright.
- Use the package manager's create form for non-npm projects (`pnpm create playwright`, etc.) so it detects
  and installs with the right tool.
- Keep the example spec — the verify step in stage 6 runs it to prove the chain works.

If browsers didn't install (e.g. `--quiet` was overridden, or a restricted environment), run
`npx playwright install` yourself.

## 4. package.json scripts

Wire the tools into scripts the user will actually run — and **chain them intelligently against what's
already there** rather than dumping five fixed entries on top of the existing set. Read the current
`scripts` block first, then:

Add the per-tool entries, using the bare binary names (not `npm run …`) so the scripts stay
package-manager-agnostic:

```json
{
	"format": "prettier --write .",
	"format:check": "prettier --check .",
	"lint": "eslint .",
	"lint:fix": "eslint . --fix"
}
```

Reconcile the test scripts with what exists — this is where "be smart" matters most:

- **No `test` script**, or it's npm's placeholder (`echo \"Error: no test specified\" && exit 1`): make
  Playwright the project's test story — `"test": "playwright test"`. Don't add a redundant `test:e2e`.
- **A real `test` script already runs a unit runner** (jest, vitest, `node --test`, mocha): leave it be and
  add `"test:e2e": "playwright test"`, so unit and e2e stay separate commands.

Then add **one** composite gate that chains everything fail-fast, cheapest check first (format is
instant, lint is quick, e2e is slowest — no point running the browser suite if formatting already fails):

```json
{
	"check": "prettier --check . && eslint . && playwright test"
}
```

If a separate unit `test` exists, weave it in ahead of the e2e run (`… && eslint . && <unit test cmd> &&
playwright test`) so the gate covers the whole project. If `format`, `lint`, or `check` names are already
taken by commands that do something different, don't overwrite them — report the collision and let the user
decide on a name, rather than silently changing their build.

## 5. Editor / IDE settings

So the tools fire in the editor, not just on the command line. Merge into existing `.vscode` files — extend
the arrays and objects, never replace them.

**`.vscode/settings.json`** — format on save via Prettier, and let ESLint auto-fix on save. VS Code needs a
separate `defaultFormatter` block per language (it rejects combined `[javascript][typescript]` syntax), so
add one block per language the project actually uses (include `[typescriptreact]`/`[vue]`/etc. as detected):

```json
{
	"editor.formatOnSave": true,
	"editor.defaultFormatter": "esbenp.prettier-vscode",
	"editor.codeActionsOnSave": { "source.fixAll.eslint": "explicit" },
	"[javascript]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
	"[typescript]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
	"[json]": { "editor.defaultFormatter": "esbenp.prettier-vscode" }
}
```

**`.vscode/extensions.json`** — recommend the matching extensions so a teammate opening the repo is prompted
to install them:

```json
{
	"recommendations": ["esbenp.prettier-vscode", "dbaeumer.vscode-eslint", "ms-playwright.playwright"]
}
```

## 6. Verify

Prove the wiring end to end — files existing isn't the same as the chain working. Run:

```bash
npm run format
npm run check
```

`format` writes any outstanding formatting so `format:check` inside `check` will pass; `check` then runs
lint and the Playwright example spec. Expect the example test to pass on a fresh setup. Fix real failures
before finishing — a lint error in generated config, a Playwright browser that didn't download — but don't
chase cosmetic reformatting Prettier already settled.

## Wrap up

Report concisely: what you detected (package manager, TS/JS, framework, anything pre-existing you skipped),
what you installed and configured, how the scripts ended up chained (especially the `test`/`test:e2e`
decision), and any judgement call the user should know about — a config left untouched because it was
hand-edited, a name collision you didn't resolve, a browser install that needs re-running.
