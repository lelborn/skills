# Ruleset reference

Exact JSON shapes for a GitHub repository ruleset, plus worked examples. Source of truth: GitHub REST API, `POST /repos/{owner}/{repo}/rulesets` (docs.github.com/en/rest/repos/rules). The same JSON is accepted by the UI's **Import a ruleset**.

## Top-level shape

```json
{
	"name": "protect-main",
	"target": "branch",
	"enforcement": "active",
	"bypass_actors": [],
	"conditions": {
		"ref_name": {
			"include": ["~DEFAULT_BRANCH"],
			"exclude": []
		}
	},
	"rules": []
}
```

- **`name`** — kebab-case (also the filename).
- **`target`** — `branch` | `tag` | `push`. Defaults to `branch`.
- **`enforcement`** — `active` | `evaluate` (org-only dry-run) | `disabled`. Required.
- **`bypass_actors`** — array; see below. Omit or `[]` for none.
- **`conditions`** — `branch`/`tag` only. `ref_name.include` / `exclude` take full refs (`refs/heads/main`, `refs/tags/v*`, fnmatch allowed) plus the specials `~DEFAULT_BRANCH` and `~ALL`. **Push rulesets take no `conditions`** — they apply to every push.
- **`rules`** — array of `{ "type": ..., "parameters": {...} }`. Rules with no parameters omit the `parameters` key.

## bypass_actors

```json
{ "actor_id": 234, "actor_type": "Team", "bypass_mode": "always" }
```

- **`actor_type`** — `Team` | `Integration` (a GitHub App) | `OrganizationAdmin` | `RepositoryRole` | `DeployKey` | `User`.
- **`bypass_mode`** — `always` or `pull_request` (bypass only when merging via PR).
- **`actor_id`** — numeric and environment-specific. Do **not** hardcode role/team/app IDs; resolve with `gh api` (`/orgs/{org}/teams`, `/repos/{owner}/{repo}/installations`, `/users/{u}`) or leave `bypass_actors: []` and add them in the UI after import.

## Rule catalogue

### Restriction rules (branch & tag)

| type                      | parameters                                             |
| ------------------------- | ------------------------------------------------------ |
| `creation`                | none — blocks creating matching refs                   |
| `deletion`                | none — blocks deleting matching refs                   |
| `update`                  | `{ "update_allows_fetch_and_merge": bool }` (required) |
| `non_fast_forward`        | none — **blocks force pushes**                         |
| `required_linear_history` | none                                                   |
| `required_signatures`     | none — require signed commits                          |

### Pull requests & merges (branch)

```json
{
	"type": "pull_request",
	"parameters": {
		"required_approving_review_count": 1,
		"dismiss_stale_reviews_on_push": true,
		"require_code_owner_review": false,
		"require_last_push_approval": false,
		"required_review_thread_resolution": true,
		"allowed_merge_methods": ["merge", "squash", "rebase"]
	}
}
```

`merge_queue` also exists (`check_response_timeout_minutes`, `grouping_strategy` ALLGREEN|HEADGREEN, `merge_method` MERGE|SQUASH|REBASE, `min_entries_to_merge`, `max_entries_to_build`, …) — only when they actually use a merge queue.

### Status checks & deployments (branch)

```json
{
	"type": "required_status_checks",
	"parameters": {
		"required_status_checks": [{ "context": "build" }],
		"strict_required_status_checks_policy": true
	}
}
```

- Each check is `{ "context": "<name>", "integration_id": <optional app id> }`. Set `integration_id` only if resolved; otherwise omit it (matches any provider reporting that context).
- `strict_required_status_checks_policy: true` = branch must be up to date before merging.
- `required_deployments`: `{ "required_deployment_environments": ["production"] }`.

### Metadata / pattern rules

`commit_message_pattern`, `commit_author_email_pattern`, `committer_email_pattern`, `branch_name_pattern`, `tag_name_pattern` — all share:

```json
{
	"type": "tag_name_pattern",
	"parameters": {
		"operator": "regex",
		"pattern": "^v\\d+\\.\\d+\\.\\d+$",
		"name": "semver tags only",
		"negate": false
	}
}
```

`operator` — `starts_with` | `ends_with` | `contains` | `regex`. `name` and `negate` optional.

### Push rulesets (target `push`)

| type                         | parameters                                         |
| ---------------------------- | -------------------------------------------------- |
| `file_path_restriction`      | `{ "restricted_file_paths": ["secrets/**"] }`      |
| `file_extension_restriction` | `{ "restricted_file_extensions": ["exe", "dll"] }` |
| `max_file_size`              | `{ "max_file_size": 100 }` (MB)                    |
| `max_file_path_length`       | `{ "max_file_path_length": 255 }`                  |

## Worked examples

### `protect-main.json` — a strict default branch

```json
{
	"name": "protect-main",
	"target": "branch",
	"enforcement": "active",
	"bypass_actors": [],
	"conditions": { "ref_name": { "include": ["~DEFAULT_BRANCH"], "exclude": [] } },
	"rules": [
		{ "type": "deletion" },
		{ "type": "non_fast_forward" },
		{
			"type": "pull_request",
			"parameters": {
				"required_approving_review_count": 1,
				"dismiss_stale_reviews_on_push": true,
				"require_code_owner_review": false,
				"require_last_push_approval": false,
				"required_review_thread_resolution": true,
				"allowed_merge_methods": ["squash", "merge"]
			}
		},
		{
			"type": "required_status_checks",
			"parameters": {
				"required_status_checks": [{ "context": "build" }],
				"strict_required_status_checks_policy": true
			}
		}
	]
}
```

### `protect-release-tags.json` — lock down version tags

```json
{
	"name": "protect-release-tags",
	"target": "tag",
	"enforcement": "active",
	"bypass_actors": [],
	"conditions": { "ref_name": { "include": ["refs/tags/v*"], "exclude": [] } },
	"rules": [
		{ "type": "deletion" },
		{ "type": "update", "parameters": { "update_allows_fetch_and_merge": false } },
		{ "type": "required_signatures" },
		{
			"type": "tag_name_pattern",
			"parameters": { "operator": "regex", "pattern": "^v\\d+\\.\\d+\\.\\d+$", "name": "semver tags only" }
		}
	]
}
```

### `block-large-files.json` — a push ruleset

```json
{
	"name": "block-large-files",
	"target": "push",
	"enforcement": "active",
	"bypass_actors": [],
	"rules": [
		{ "type": "max_file_size", "parameters": { "max_file_size": 100 } },
		{ "type": "file_extension_restriction", "parameters": { "restricted_file_extensions": ["zip", "exe", "dll"] } }
	]
}
```
