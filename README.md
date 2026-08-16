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

## Skills

- **[conventional-commits](skills/conventional-commits/SKILL.md)** — Write git commit messages that follow the [Conventional Commits v1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) spec.
- **[keep-a-changelog](skills/keep-a-changelog/SKILL.md)** — Write, update, and review a `CHANGELOG.md` following [Keep a Changelog 1.1.0](https://keepachangelog.com/en/1.1.0/). Changelog and version proposal only; tags and GitHub releases need explicit approval.
- **[semver](skills/semver/SKILL.md)** — Apply [Semantic Versioning 2.0.0](https://semver.org/) to version bumps, validation, and precedence. Pairs with the two skills above.
- **[keep-it-human](skills/keep-it-human/SKILL.md)** — Rewrite text to strip the tells of AI-generated writing so it reads like a person wrote it, without changing the meaning.
- **[handoff](skills/handoff/SKILL.md)** — Compact the current session into a handoff document so a fresh agent can continue without re-reading the transcript. Run it with `/handoff`.
- **[comment-code](skills/comment-code/SKILL.md)** — Comment a targeted source file: the why, not the what. Also audits existing comments (read-only) on request.
- **[review-file](skills/review-file/SKILL.md)** — Deep review of one named file. Produces a severity-ranked report and changes nothing until you ask.
- **[challenge](skills/challenge/SKILL.md)** — Stress-test a plan, decision, or idea with one sharp question at a time, then land on a sharpened plan.
- **[sc-init](skills/sc-init/SKILL.md)** — Initialise a NetSuite SuiteCloud (SDF) Account Customization project: agent skills, Prettier, ESLint, package.json scripts, .gitignore, README, docs, and CHANGELOG.
- **[tooling-init](skills/tooling-init/SKILL.md)** — Set up Prettier, ESLint, and Playwright on a JavaScript/TypeScript project after evaluating what's already there. Safe to re-run.

Changes are logged in the [changelog](CHANGELOG.md).

## Before you install

A skill is a set of instructions your AI agent will act on, so have a read of the `SKILL.md` before installing. That goes for mine or anyone else's, as people can abuse these files. Read more via [Snyk](https://snyk.io/blog/toxicskills-malicious-ai-agent-skills-clawhub/).

These are mine, shared in case they're useful to you too. No support, no warranty. They change when I change my mind.

Nothing here is affiliated with or endorsed by Anthropic or any other named tool.

## Found something?

Found something off in a skill? Open an issue. I'd rather hear about it than not.

## License

[GPL-3.0](LICENSE)
