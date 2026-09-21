# Skills

Skills I use and build for myself, kept here so I stop rewriting them from scratch. Help yourself.
They run in Claude Code, Cursor, Copilot, and anything else the [`skills`](https://skills.sh) CLI supports.

## Install

Add them all:

```sh
npx skills add lelborn/skills
```

Or just the one you're after:

```sh
npx skills add lelborn/skills --skill conventional-commits
```

Add `-g` if you'd rather have them everywhere than in the one project.

## What's here

Grouped by what they help with. Each name links to its `SKILL.md` — the full description and detail live there.

### Commits, changelogs & versions

| Skill                                                        | What it does                                                |
| ------------------------------------------------------------ | ----------------------------------------------------------- |
| [conventional-commits](skills/conventional-commits/SKILL.md) | Commit messages that follow Conventional Commits v1.0.0.    |
| [keep-a-changelog](skills/keep-a-changelog/SKILL.md)         | Maintain a `CHANGELOG.md` following Keep a Changelog 1.1.0. |
| [semver](skills/semver/SKILL.md)                             | Version bumps, validation, and precedence (SemVer 2.0.0).   |

### GitHub

| Skill                                        | What it does                                                        |
| -------------------------------------------- | ------------------------------------------------------------------- |
| [gh-protect](skills/gh-protect/SKILL.md)     | Build a repository ruleset as JSON under `.github/.rulesets/`.      |
| [gh-templates](skills/gh-templates/SKILL.md) | Scaffold issue and PR templates in a Conventional-Commit style.     |
| [gh-depbot](skills/gh-depbot/SKILL.md)       | Configure `.github/dependabot.yml` from the repo's real ecosystems. |
| [gh-issues](skills/gh-issues/SKILL.md)       | Working standard for writing and managing issues.                   |
| [gh-prs](skills/gh-prs/SKILL.md)             | Working standard for opening, reviewing, and merging PRs.           |

### Docs

| Skill                                  | What it does                                             |
| -------------------------------------- | -------------------------------------------------------- |
| [docs-init](skills/docs-init/SKILL.md) | Scaffold a `.docs/` tree, sized to the project.          |
| [doc-it](skills/doc-it/SKILL.md)       | Write or update one doc page and keep the index in sync. |
| [doc-check](skills/doc-check/SKILL.md) | Audit `.docs/` for leaks, drift, broken links, and gaps. |

### Code & prose quality

| Skill                                          | What it does                                                 |
| ---------------------------------------------- | ------------------------------------------------------------ |
| [comment-code](skills/comment-code/SKILL.md)   | Add the comments that earn their place; audit existing ones. |
| [review-file](skills/review-file/SKILL.md)     | Deep, severity-ranked review of one named file.              |
| [keep-it-human](skills/keep-it-human/SKILL.md) | Strip the tells of AI-generated writing.                     |

### Project setup

| Skill                                        | What it does                                                |
| -------------------------------------------- | ----------------------------------------------------------- |
| [sc-init](skills/sc-init/SKILL.md)           | Initialise a NetSuite SuiteCloud (SDF) project.             |
| [tooling-init](skills/tooling-init/SKILL.md) | Set up Prettier, ESLint, and Playwright on a JS/TS project. |

### Sessions & this book

| Skill                                          | What it does                                                          |
| ---------------------------------------------- | --------------------------------------------------------------------- |
| [handoff](skills/handoff/SKILL.md)             | Compact a session into a handoff document (`/handoff`).               |
| [challenge](skills/challenge/SKILL.md)         | Stress-test a plan one sharp question at a time.                      |
| [archive-skill](skills/archive-skill/SKILL.md) | Retire a skill to `.archive/` without deleting it (`/archive-skill`). |

Changes are logged in the [changelog](CHANGELOG.md).

## Before you install

A skill is a set of instructions your AI agent will act on, so have a read of the `SKILL.md` before installing. That goes for mine or anyone else's, as people can abuse these files. Read more via [Snyk](https://snyk.io/blog/toxicskills-malicious-ai-agent-skills-clawhub/).

These are mine, shared in case they're useful to you too. No support, no warranty. They change when I change my mind.

Nothing here is affiliated with or endorsed by Anthropic or any other named tool.

## Found something?

Found something off in a skill? Open an issue. I'd rather hear about it than not.

## License

[GPL-3.0](LICENSE)
