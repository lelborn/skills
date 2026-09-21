# dependabot.yml reference

Schema for `.github/dependabot.yml` version updates. Source of truth: GitHub docs, "Configuration options for the dependabot.yml file" (docs.github.com/en/code-security/dependabot). `version: 2` and `updates:` are required.

## package-ecosystem → what to detect

| `package-ecosystem`         | Detect from                                                             |
| --------------------------- | ----------------------------------------------------------------------- |
| `npm`                       | `package.json` (npm, pnpm, yarn)                                        |
| `bun`                       | `bun.lockb` / `bun.lock`                                                |
| `pip` / `uv`                | `requirements*.txt`, `pyproject.toml`, `Pipfile` (`uv` for uv projects) |
| `bundler`                   | `Gemfile`                                                               |
| `composer`                  | `composer.json`                                                         |
| `cargo`                     | `Cargo.toml`                                                            |
| `gomod`                     | `go.mod`                                                                |
| `gradle`                    | `build.gradle` / `build.gradle.kts`                                     |
| `maven`                     | `pom.xml`                                                               |
| `nuget` / `dotnet-sdk`      | `.csproj`, `packages.config`, `global.json`                             |
| `mix`                       | `mix.exs`                                                               |
| `hex`                       | Elixir/Erlang Hex                                                       |
| `pub`                       | `pubspec.yaml` (Dart/Flutter)                                           |
| `swift`                     | `Package.swift`                                                         |
| `terraform` / `opentofu`    | `*.tf`                                                                  |
| `docker` / `docker-compose` | `Dockerfile` / `docker-compose.yml`                                     |
| `helm`                      | `Chart.yaml`                                                            |
| `gitsubmodule`              | `.gitmodules`                                                           |
| `devcontainers`             | `.devcontainer/`                                                        |
| `github-actions`            | `.github/workflows/*` (always add this one)                             |

Others GitHub supports: `bazel`, `conda`, `deno`, `elm`, `julia`, `nix`, `sbt`, `vcpkg`.

## `updates` entry keys

**Required:** `package-ecosystem`, `directory` **or** `directories`, `schedule.interval`.

- **`directory`** — single path (`/`, `/app`). No globs.
- **`directories`** — list; supports `*` globs (`["/apps/*", "/"]`). Prefer for monorepos.
- **`schedule`** — `interval`: `daily` | `weekly` | `monthly` (also `quarterly`, `semiannually`, `yearly`, or `cron`). Optional `day` (for weekly), `time` (`HH:MM`), `timezone`.
- **`commit-message`** — `prefix` (e.g. `chore`), `prefix-development` (separate prefix for dev deps), `include: scope` (appends the dependency scope).
- **`open-pull-requests-limit`** — cap concurrent version-update PRs (default 5). `0` disables version updates while leaving security updates on.
- **`groups`** — see below. The main lever for reducing PR noise.
- **`labels`** — list of labels to apply (must exist in the repo).
- **`assignees`**, **`reviewers`**, **`milestone`** — PR metadata (`milestone` is a numeric id).
- **`target-branch`** — open PRs against a non-default branch.
- **`allow`** / **`ignore`** — filter by `dependency-name` (glob), `dependency-type` (`direct`/`indirect`/`production`/`development`), `versions`, `update-types` (e.g. `version-update:semver-major`).
- **`versioning-strategy`** — `auto` | `increase` | `increase-if-necessary` | `lockfile-only` | `widen`.
- **`rebase-strategy`** — `auto` | `disabled`.
- **`cooldown`** — delay before opening PRs for newly released versions (version updates only).
- **`vendor`** — `true` to update vendored deps.
- **`pull-request-branch-name`** — `{ separator: "-" }`.
- **`registries`** — names of top-level registries this entry may use (or `"*"`).

## groups

Consolidate many dependency bumps into one PR. First matching group wins; a dep matching both `patterns` and `exclude-patterns` is excluded.

```yaml
groups:
    <group-name>:
        applies-to: version-updates # or security-updates
        dependency-type: production # or development
        patterns: ['react*', 'lib-*']
        exclude-patterns: ['*-test']
        update-types: ['minor', 'patch']
```

## Private registries (never inline secrets)

```yaml
registries:
    npm-private:
        type: npm-registry
        url: https://npm.pkg.github.com
        token: ${{ secrets.DEPENDABOT_NPM_TOKEN }}
updates:
    - package-ecosystem: npm
      directory: /
      registries:
          - npm-private
      schedule:
          interval: weekly
```

The `token`/`password` value must reference a **Dependabot secret** (`${{ secrets.NAME }}`) created in repo or org settings — never a literal credential.

## Examples

### House default — grouped minor/patch, `chore` commits

```yaml
version: 2
updates:
    - package-ecosystem: github-actions
      directory: /
      schedule:
          interval: weekly
      commit-message:
          prefix: chore
          include: scope
      groups:
          actions:
              patterns: ['*']
              update-types: ['minor', 'patch']

    - package-ecosystem: npm
      directory: /
      schedule:
          interval: weekly
      commit-message:
          prefix: chore
          include: scope
      groups:
          npm-minor-patch:
              patterns: ['*']
              update-types: ['minor', 'patch']
```

Majors aren't grouped here, so each arrives as its own PR for individual review.

### Monorepo with globbed directories

```yaml
version: 2
updates:
    - package-ecosystem: npm
      directories: ['/', '/packages/*']
      schedule:
          interval: weekly
      commit-message:
          prefix: chore
          include: scope
      open-pull-requests-limit: 10
```
