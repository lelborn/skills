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

- **[conventional-commits](skills/conventional-commits/SKILL.md)** — Write git commit messages that follow the [Conventional Commits v1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) spec. Covers type selection, scope, breaking changes, reviewing messages, and generating a changelog from history.
- **[keep-a-changelog](skills/keep-a-changelog/SKILL.md)** — Write, update, and review a `CHANGELOG.md` following [Keep a Changelog 1.1.0](https://keepachangelog.com/en/1.1.0/). Covers the six change types, cutting releases (changelog and version proposal only — tags and GitHub releases need explicit approval), semver from `package.json`, yanked releases, and compliance audits.
- **[semver](skills/semver/SKILL.md)** — Apply [Semantic Versioning 2.0.0](https://semver.org/) to choose version bumps, validate version strings, and compare precedence. Pairs with the two skills above to keep commits, versions, and changelog in sync.
- **[keep-it-human](skills/keep-it-human/SKILL.md)** — Rewrite text to strip the tells of AI-generated writing so it reads like a person wrote it, without changing the meaning. Catalogues the common tells, gives a rewrite process, matches your voice from samples, and guards against overcorrecting.
- **[handoff](skills/handoff/SKILL.md)** — Compact the current session into a concise handoff document so a fresh agent or session can continue without re-reading the whole transcript. Run it with `/handoff`; captures goal, current state, next steps, dead ends, and references, with secrets redacted.
- **[comment-code](skills/comment-code/SKILL.md)** — Comment a targeted source file constructively and concisely: detect the language, then add only the comments that earn their place — the why, not the what. Also audits existing comments (read-only) on request. Never touches logic, leaves good comments alone, and flags where clearer code would beat a comment.
- **[review-file](skills/review-file/SKILL.md)** — Deep review of one named file: structure, readability, security, comments, external API alignment, project docs, and split/cohesion. Produces a severity-ranked report and changes nothing until you ask.
- **[challenge](skills/challenge/SKILL.md)** — Stress-test a plan, decision, or idea by interrogating it hard — one sharp question at a time, each with a recommended answer — surfacing hidden assumptions, failure modes, and expensive-to-reverse choices, then landing on a sharpened plan.
- **[sc-init](skills/sc-init/SKILL.md)** — Initialise a new or empty NetSuite SuiteCloud (SDF) Account Customization project: install agent skills, set up Prettier and ESLint, write package.json scripts, .gitignore, README, docs and CHANGELOG, then git init.

Check back for new or updated skills. Changes are logged in the [changelog](CHANGELOG.md).

## Before you install

A skill is a set of instructions your ai agent will act on, so have a read of the `SKILL.md` before installing. That goes for mine or anyone else's, as people can abuse these files. Read more via [Snyk](https://snyk.io/blog/toxicskills-malicious-ai-agent-skills-clawhub/).

These are mine, shared in case they're useful to you too. No support, no warranty. They change when I change my mind.

Nothing here is affiliated with or endorsed by Anthropic or any other named tool.

## Found something?

Found something off in a skill? Open an issue. I'd rather hear about it than not.

## License

[GPL-3.0](LICENSE)
