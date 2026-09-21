---
name: gh-protect
description: Wizard for building a GitHub repository ruleset (Settings → Rules → Rulesets — the modern replacement for branch protection). Interviews the user, then writes a valid ruleset JSON to `.github/.rulesets/<name>.json` for them to import in the UI or apply with `gh api`. Handles branch, tag, and push rulesets. Use when the user runs /gh-protect or wants to protect a branch or tag, require pull requests / reviews / status checks / signed commits, block force pushes or deletions, restrict pushed files, or "set up a ruleset / branch protection". Names rulesets in kebab-case (e.g. `protect-main`). Produces the file and guides applying it; never changes repo settings without explicit confirmation.
argument-hint: What to protect (e.g. main branch, release tags, large files)
---

# gh-protect

Walk the user through building a **GitHub repository ruleset** and save it as JSON they can apply. Rulesets are GitHub's current mechanism for protecting refs and pushes (Settings → Rules → Rulesets), superseding classic branch protection. The output of this skill is a ruleset JSON file under `.github/.rulesets/`; applying it to the repo is a separate, explicitly-confirmed step — this skill never changes repo settings on its own.

The full rule catalogue, exact JSON parameter shapes, and complete example rulesets live in [references/rules.md](references/rules.md) — read it before writing any rule you're not certain of, rather than guessing a parameter name.

## Interview, then build

Ask only what's needed for the ruleset the user actually wants; don't march through every option. Suggest sensible defaults and let them override. Cover:

1. **Target type** — `branch`, `tag`, or `push`:
    - **branch** — protect branches: require PRs, reviews, status checks, signed commits; block force-push/deletion.
    - **tag** — protect tags: block deletion/update, enforce a tag-name pattern, require signatures.
    - **push** — restrict what can be pushed repo-wide: file paths, extensions, size, path length. (No ref conditions — push rulesets apply to all pushes.)
2. **Name** — kebab-case and sensical, used for both the filename and the ruleset's `name`. Suggest something like `protect-main`, `protect-release-tags`, or `block-large-files`. Validate it's kebab-case (`^[a-z0-9]+(-[a-z0-9]+)*$`); if the user offers something else, propose the kebab-case form.
3. **Conditions** (branch/tag only) — which refs it applies to: the default branch (`~DEFAULT_BRANCH`), all (`~ALL`), or fnmatch patterns (`refs/heads/main`, `refs/heads/release/*`, `refs/tags/v*`). Support include and exclude lists.
4. **Enforcement** — `active` (enforced), `evaluate` (dry-run, reports without blocking — organisation repos only), or `disabled`. Default `active`; suggest `evaluate` first when they want to trial a strict ruleset on an org repo.
5. **Bypass list** (optional) — who may bypass, and whether always or for pull requests only. Bypass actors are identified by numeric IDs that are specific to the repo/org (team, app, role, user), so **don't invent them** — either resolve them with `gh api` (e.g. `gh api /orgs/{org}/teams`) or leave the bypass list empty and tell the user to add bypass actors in the UI after import. Default: no bypass (strictest).
6. **Rules** — pick from the catalogue in [references/rules.md](references/rules.md) the ones that fit the target. For a typical `protect-main`, a good default is: require a pull request (1 approval, dismiss stale reviews, require conversation resolution), block force pushes, block deletion, and — if they use CI — require status checks. Offer signed commits and linear history as stricter add-ons.

## Where the file goes

Ensure `.github/` and then `.github/.rulesets/` exist at the repo root — **create whichever is missing, reuse them if present** — and write the ruleset to `.github/.rulesets/<name>.json`. One ruleset per file, pretty-printed (one key per line, indented), named to match the ruleset. The JSON is shaped as a REST create payload (see the example in [references/rules.md](references/rules.md)); that same file is what GitHub's "Import a ruleset" accepts.

## Applying it (only when the user confirms)

Creating or changing a ruleset is a change to repository settings — present both paths and **wait for an explicit yes** before running anything that writes to GitHub:

- **UI import** (matches how the user framed this): Settings → Rules → Rulesets → **New ruleset ▸ Import a ruleset**, then choose the JSON file. Resolve the repo from the git remote so you can give them the exact `https://github.com/<owner>/<repo>/settings/rules` link.
- **CLI:** resolve `<owner>/<repo>` from the git remote, then
    ```sh
    gh api --method POST /repos/<owner>/<repo>/rulesets --input .github/.rulesets/<name>.json
    ```
    This creates a **new** ruleset each time. To update one that already exists, find its id (`gh api /repos/<owner>/<repo>/rulesets`) and use `--method PUT /repos/<owner>/<repo>/rulesets/<id>`.

After a successful apply, remind them that bypass actors and status-check app IDs may still need setting in the UI if they were left as placeholders.

## Guardrails

- **Produce the file; don't apply without a clear yes.** Writing the JSON is the default end of the job.
- **Never fabricate numeric IDs** (`actor_id`, `integration_id`). Resolve them or leave a clearly-marked placeholder and say so.
- **Resolve owner/repo from the git remote**, never a guessed default.
- **One ruleset per file, kebab-case name.** Don't bundle several rulesets into one JSON.
- Note the plan gate when relevant: private repositories need GitHub Pro/Team/Enterprise for rulesets, and `evaluate` enforcement is organisation-only.
