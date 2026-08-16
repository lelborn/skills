---
name: comment-code
description: Add or review comments on a specific source file — detect the language, then add only the comments that earn their place (the why, not the what), or audit existing comments and report what's unclear. Use when the user asks to comment, annotate, or document a file, add docstrings or JSDoc, or check/review comments for clarity or ambiguity. Works on the named file(s) only, never a whole-repo sweep. Trigger on "comment this file", "add comments", "document this", "check these comments", or "are these comments clear".
---

# Comment Code

Add comments to a source file that a competent reader would be glad of — and nothing more. The bar is high on purpose: most comments are noise, and noisy comments are worse than none, because they go stale, bury the few that matter, and narrate what the code already says. Add the ones that carry their weight, and leave everything else — including comments already doing their job — untouched.

## Scope: only what you were pointed at

Work on the specific file (or files) the user named, and no others. Don't fan out to siblings, imports, or the rest of the repo because they "could use comments too" — that unasked-for sweep is exactly the churn that makes this skill annoying to use. If the target is unclear, or you were handed several files, confirm which before editing.

## Two jobs — know which one you're doing

- **Commenting** edits the file: you add or fix comments on the target.
- **Reviewing / checking** is read-only by default: when the user asks you to _check_, _review_, _verify_, or _look for ambiguity or clarity_ in comments, that is an audit, not a rewrite. Read the file, report a short list of the specific spots with a real problem (with line references), and stop. Then edit only the spots you flagged — and only after the user says go, unless the fix is trivial and unambiguous.

Conflating these is the classic failure: asked to "check for ambiguity," an agent re-processes and rewrites every comment in every file. Don't. Checking means reading and reporting; editing means changing the few things that are genuinely wrong. A request to review comments is never a licence to re-touch the whole file.

## First, read and identify

Read the whole target file before writing or judging a comment — you can't tell what's non-obvious without the context. (If no file was named, ask which one.) Identify the language from its extension and syntax so you use the right comment tokens and, for public symbols, its doc convention: JSDoc `/** */`, Python `"""docstrings"""`, Rust `///`, Go `// Name …`, Javadoc, and so on. Match the file's existing comment style — consistency beats your preference.

## Comment the why, never the what

Code already says _what_ it does; a comment that restates it (`i++ // increment i`) is pure cost. Spend comments on what the code _can't_ say:

- **Intent / rationale** — why this approach, and why not the obvious alternative.
- **Non-obvious decisions and trade-offs** — the constraint or context that forced this shape.
- **Gotchas and invariants** — "must stay sorted", "callers hold the lock", "order matters here".
- **Edge cases** — why a branch exists, what input provokes it.
- **Workarounds** — the bug or limitation being handled, with an issue link where possible.
- **Public contracts** — for exported symbols, a short doc comment: what it's for, its inputs/outputs, any surprising behaviour.

If the only honest comment you could write restates the code, that's a signal the _code_ is unclear — the fix is a clearer name or an extracted function, not a comment papering over it (see the `refactor` skill). Say so rather than adding a "what" comment.

## Be brief and well-placed

- **Brevity** — the fewest words that land the point; one sharp line beats three. Cut filler.
- **Placement** — a doc comment above the declaration for API-level things; a short block above a tricky section; an inline comment only for the single line that needs it.
- **Density** — a handful of well-aimed comments per file is usually right. If every function has one, most are noise.

## Once a comment is good, leave it alone

A comment that already carries its weight is finished work. Don't rephrase it into your own style, and don't re-touch a file that's already well commented — running this skill again on clean code should be close to a no-op. Every edit needs a concrete reason you could say out loud: _this one is ambiguous / stale / wrong / a bare restatement_. If you can't name what's wrong with a comment, that is your answer — it stays.

This is what confident commenting looks like: make the call, then trust it instead of churning the file. Second-guessing a sound comment into a rewrite adds a noisy diff and erodes trust, and gains nothing. A near-empty diff on a well-commented file is a success, not a sign you missed something.

## Don't touch the code; clean as you go

Only add or edit comments — don't change logic, formatting, or names here (that's a separate task). Do remove existing comments that are false, redundant, or commented-out cruft, since a wrong comment is worse than none. Flag what you removed.

## Wrap up

Note briefly what you changed and why — or, for a review, the list of issues found — and flag anywhere the code was confusing enough that a rename or refactor would serve the reader better than any comment.
