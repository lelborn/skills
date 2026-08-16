---
name: handoff
description: Compact the current conversation into a concise handoff document so a fresh agent or session can pick the work up without re-reading the whole transcript. Use when the user runs /handoff, says they're wrapping up, wants to continue in a new session, is about to hit a context limit, or asks to "hand this off", "write a handoff", or "summarise where we are for the next session".
argument-hint: What will the next session be used for?
disable-model-invocation: true
---

# Handoff

Turn what matters about the current session into a short document a fresh agent can read in under a minute and keep working from — no replaying the conversation. A fresh agent handed the raw transcript wastes budget re-deriving what matters and can't tell a settled decision from a passing idea, or a live approach from an abandoned one. The handoff is the signal with the noise removed. Run it deliberately (hence no auto-invocation) — when wrapping up, switching sessions, or nearing a context limit.

## How to write it

Work from the actual conversation. Pull out the through-line — what we set out to do, where we got to, what's next — and:

- **Reference, don't restate.** If a spec, PR, issue, commit, or doc exists, point to it by path/URL and say what it is; don't paste it (that just creates a second copy that drifts). The thing worth capturing that _isn't_ already written down is the reasoning behind decisions.
- **Capture dead ends.** The biggest waste in a fresh session is confidently repeating a failed approach. Record what was tried and why it didn't work.
- **Tailor to the next session's purpose** (the user's argument). Let it steer emphasis, especially Next steps. No argument → assume it continues the current work.
- **Redact secrets and personal data.** These docs get pasted around and sometimes committed. Reference by name ("the Stripe key in `.env`"), never by value.
- **Keep it tight.** Drop any section with nothing real in it; a few lines is a complete handoff for a small session. Don't pad to fill the template.

## Document structure

Drop any section that doesn't apply:

```markdown
# Handoff: <short title>

**Date:** <YYYY-MM-DD>
**Next session focus:** <the user's stated purpose, or "continue current work">

## Goal

<1–3 sentences: what we're trying to achieve, and why.>

## Current state

<What's done and confirmed working; what's in progress. Be explicit about what's
unverified or assumed rather than tested.>

## Next steps

1. <First concrete action — specific enough to start immediately.>

## Key decisions & context

- <A decision and its reason, so it isn't relitigated.>
- <A constraint or gotcha to respect.>

## Dead ends

- <An approach tried and why it failed — don't redo this.>

## Artifacts & references

- `path/to/file` — <what it is / why it matters>
- <PR / issue / doc URL> — <status>
- <key commands to build, run, or test>

## Open questions / blockers

- <Anything unresolved or waiting on the user.>

## Suggested skills

- `skill-name` — <when the next agent should reach for it>
```

The **Suggested skills** section primes the next session to reach for the right tool instead of starting from scratch — list only skills that genuinely fit the next steps.

## Where to save it

Write to the OS temp directory (`$TMPDIR` or `/tmp`; `%TEMP%` on Windows), not the repo or working directory, so it isn't committed by accident — filename like `handoff-<slug>-<YYYY-MM-DD>.md`. Then give the user the absolute path and a one-line summary. If they'd rather have it in the project (to commit intentionally), save where they ask.

## Example

```markdown
# Handoff: fix flaky auth tests in CI

**Date:** 2026-07-20
**Next session focus:** confirm the fix holds across three CI runs, then close the ticket

## Current state

Root cause: tests share a module-level token a parallel test mutates. Fixed by giving each
test its own token in `tests/auth/setup.ts`. Passed locally 20×; CI not yet re-run.

## Next steps

1. Re-run CI three times; confirm all green.
2. If green, close AUTH-412 and note the root cause.

## Dead ends

- `--runInBand` hid the flake but tripled CI time — reverted.

## Artifacts & references

- `tests/auth/setup.ts` — the fix; PR .../pull/988 (open, awaiting green CI)
- Run: `pnpm test auth`

## Suggested skills

- `conventional-commits` — for the commit and PR title when merging
```
