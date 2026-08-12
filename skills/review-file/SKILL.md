---
name: review-file
description: >-
  Deep review of one named file — structure and definition order, readability,
  security, comment quality, alignment with the external APIs it calls, project
  documentation coverage, and whether any part of it should be its own module.
  Produces a severity-ranked report and changes nothing until asked. Use this
  whenever the user points at a single file and wants a considered look:
  "review-file this", "sanity-check ni_http.js", "take a closer look at this
  module", "review this for security and comments", "does this match the docs?",
  "is this documented under docs/?", "anything here that should be split out?".
  Use it even when the ask is vague ("what do you make of this file?") — a
  named file plus a request for an opinion is a file review. Loads an optional
  NetSuite/SuiteScript layer when the file is SuiteScript.
---

# Review file

Review **one file the user names** (or `@`s). Read the whole thing, run the passes
below, report, and stop. This skill does not edit anything until the user asks —
see [Apply mode](#apply-mode).

If the user names several files, review the first and offer to do the rest; a
proper file review goes deep on one file, and batching turns it into skimming.

## Optional layers

Load these only when they apply. Do not invent parallel standards — if a
companion skill exists in the repo, it wins over your own instincts.

| Situation | Load |
|---|---|
| File is SuiteScript / imports `N/*` | `references/netsuite.md` |
| Repo has a `docs/` tree | `references/project-docs.md` |
| Repo has `.agents/skills/keep-it-human/` | that skill, before writing or rewriting any comment text |
| File is auth, HTTP, Suitelet/RESTlet or credential-adjacent | `.agents/skills/netsuite-owasp-secure-coding/` if present |

## Passes

Track them so none get skipped:

```
File review on <path>:
- [ ] 1. Orient
- [ ] 2. Structure and order
- [ ] 3. Readability
- [ ] 4. Security
- [ ] 5. Comments
- [ ] 6. External API alignment
- [ ] 7. Project docs
- [ ] 8. Cohesion / split
- [ ] 9. Report, then wait
```

### 1. Orient

Read the file end to end before forming any view — a finding based on half a file
is usually wrong, and the user will spot it.

Then establish context:

- What does this file claim to do, and does the code match that claim?
- Who imports it (`require` / `define` / `import`), and which exports do they
  actually use? Unused exports and callers relying on undocumented behaviour are
  both worth knowing before you judge the structure.
- What constrains it — runtime, script type, client vs server, sandbox rules.

### 2. Structure and order

The test: can a stranger find the public API in under a minute?

Definition order should follow call flow, so reading top to bottom builds up
rather than jumping forward. A workable default:

1. Constants
2. Shared factories used by later helpers
3. Validation / guards
4. Domain helpers (auth, parse, format)
5. Entry points
6. Exports, with a one-line note where something is public for non-obvious reasons

Flag helpers that reach for bindings defined far below when a plain reorder would
read better, unrelated concerns sharing a file, and exports nothing consumes.

### 3. Readability

- One clear job per module; deeply nested branches that would flatten.
- Naming that matches the vocabulary of the domain and the rest of the repo.
- Footguns: case-sensitive lookups, magic strings, result shapes that differ
  between branches, silent falsy coercion.
- Pointed notes beat rewriting someone's style for taste. If the only argument is
  "I'd have written it differently", leave it alone.

### 4. Security

Check what applies and skip what does not — a padded security section trains the
user to ignore the real findings.

- **Secrets**: no plain tokens in source, logs or error strings; correct secret
  API for the platform.
- **Outbound calls**: HTTPS only; be explicit about *who* allowlists hosts — this
  file or its caller. Ambiguity here is itself a finding.
- **Logging**: no auth headers, request bodies, or secret-bearing URLs.
- **Input trust**: record fields, script parameters, partner payloads, URL
  fragments and anything else crossing a boundary.
- **Client vs server**: secret handling is server-side only.
- **Error messages**: echoing a partner response body leaks more than intended —
  check truncation and sensitivity.

### 5. Comments

A good comment says what breaks if the next person skips a step, or why a
non-obvious platform quirk exists. Everything else is noise.

Want: a blunt file header stating the job and its constraints; notes on trust
boundaries and intentional non-goals; warnings about quirks that look like bugs.

Do not want: line-by-line narration; restating the function name in prose; AI
filler ("robust", "seamless", "ensures that", em-dash piles, bold lead-ins).

Load `keep-it-human` before rewriting any comment text.

### 6. External API alignment

For every external API the file calls, verify against real documentation rather
than memory: parameter names, enum values, return shapes, error codes, and which
runtimes support the call.

Two things matter more than they look:

- **Intentional deviations from the sample code.** Say plainly that it deviates,
  and whether it is still valid. Silently "correcting" a deliberate choice is the
  worst outcome of a review.
- **Comments that contradict the docs.** Fix these even when the code is right —
  a wrong comment outlives the person who wrote it.

Never claim doc alignment from memory when the surface is credential-, HTTP- or
version-sensitive. Fetch, or use a bundled reference.

### 7. Project docs

If the repo has a `docs/` tree, read `references/project-docs.md` and check
coverage and drift. If it does not, skip this pass and say so in one line.

### 8. Cohesion / split

Default bias: **keep one cohesive file.** Splitting has a real cost — another
import, another place to look, another thing to keep in sync.

Suggest a new module only when a chunk is already used (or imminently will be)
without the rest of this file's job, or when a second concern has grown large
enough to crowd the main path. Private helpers with one caller stay put.

"No split needed" is a complete and frequently correct answer. Say it.

### 9. Report, then wait

Open with a verdict of one or two sentences — the honest overall read, not a
warm-up. Then the sections below, in this order, dropping any that produced
nothing:

```
**Verdict:** <one or two sentences>

**Holds up well**
- <what is genuinely good, briefly — this is not flattery, it tells the user
  what not to touch>

**Structure and order**
**Readability**
**Security**
**Comments**
**External API alignment**
**Project docs**
**Split**

**If you want me to apply anything**
1. <numbered, so the user can reply "do 1 and 3">
```

Tag every finding:

- **Blocker** — wrong, unsafe, or will bite in production
- **Should fix** — real cost to the next reader or maintainer
- **Consider** — a judgement call the user may reasonably decline

Rules that keep the report worth reading:

- Every finding gets a location and a concrete change. "Consider improving error
  handling" is not a finding.
- Quote the smallest snippet that makes the point. Never paste the file back.
- A pass with nothing to say gets one line — "Nothing to flag" — and never a
  manufactured finding to make the section look busy.
- Do not grade the file out of ten or award it a rating.

Tone: direct, plain, no chatbot padding. End with the apply list and stop.

## Apply mode

Only once the user asks ("yes", "apply", "do 2 and 4", "fix the comments"):

- Touch only what the review flagged, or the subset they scoped.
- Reorder only where it improves reading without changing behaviour — or where
  the behaviour change is a hardening you both agreed.
- Re-verify the docs for any API you alter.
- Match existing repo patterns when adding documentation.
- Do not commit or push unless asked.
