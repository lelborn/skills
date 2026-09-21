---
name: gh-issues
description: The working standard for writing and managing GitHub issues well — apply it whenever creating, writing, editing, triaging, commenting on, or closing an issue (via `gh`, the API, a template, or the UI), or turning a bug, feature, or task into an issue, even when not explicitly asked to "follow standards". Covers searching for duplicates first, one concern per issue, Conventional-Commit titles, writing the body as an implementable spec with a verifiable "Done when" checklist, labels and linking, comment etiquette, and never closing an issue while tasks are still unchecked.
---

# Writing GitHub issues

An issue is a small contract: it says what's wrong or wanted, and how everyone will know it's done. Written well, it's the spec someone (often an agent) implements against without needing the backstory. Written badly, it's noise that someone else has to decode, deduplicate, or reopen. This standard is how to write the good kind. Apply it to every issue you touch — it's not a step you wait to be asked for.

## Before you file

- **Search first.** Look for an existing open or recently-closed issue for the same thing. If one exists, add to it (a comment, a reproduction, a +1 with new detail) instead of opening a duplicate. Link related issues rather than restating them.
- **One issue, one concern.** A bug and a feature it inspired are two issues. If you catch yourself writing "and also", split it. Tangled issues never fully close.
- **File only what's actionable.** An issue is a discrete unit of work with a definition of done — not a vague "improve X" or a place to think out loud. If it isn't yet actionable, say what's unknown and what would make it so.
- **Use the repo's template.** If `.github/ISSUE_TEMPLATE/` exists, follow the matching template (bug / feature / chore) and fill every heading; write `n/a` rather than deleting a heading you can't fill. (The `gh-templates` skill sets these up.)

## Title

- **Prefix with the Conventional-Commit type** the work will carry: `fix:`, `feat:`, `chore:` (or `docs:`/`refactor:`/`test:`/`ci:`/`build:` when one fits). This sets the type from the first word and lines the issue up with its eventual commit and PR. (See the `conventional-commits` skill.)
- **Say the specific thing**, imperatively. `fix: crash when saving an empty profile` — not `bug`, `broken`, or `saving issue`. A title should be enough to tell two issues apart in a list.

## Body — write it as a spec

Fill the template's headings; where there's no template, cover the same ground:

- **Bug:** what happened, what should happen, exact steps to reproduce, where (component/file/screen/environment), and evidence (log, trace, screenshot) if you have it.
- **Feature:** the problem and who it blocks (not the solution yet), then the proposal, then scope boundaries.
- **Chore:** why it's worth doing and what the work is.

Write for a person reading cold: plain, concrete, no padding or diff-narration (the `keep-it-human` skill applies). State facts and reproductions over opinions; keep it neutral and blame-free.

## "Done when" — the definition of done

Every issue needs a **Done when** checklist of `- [ ]` items that are each independently verifiable — the acceptance criteria the work is measured against.

- Make each item something you can actually check (a passing test, an observable behaviour), not "works properly".
- These boxes are the spec while the work is open, and the gate when it's finishing: **tick a box only when you've truly verified it** (you ran it, or CI did, and it passed). Checking an unverified box is a violation.
- If an item stops applying, don't leave a ghost todo — edit it to `- [x]` with a one-line `(obsolete: …)` reason so the list stays honest.

## Labels, assignment & linking

- Apply the **type label** (`bug` / `enhancement` / `chore`) and only labels that exist in the repo — GitHub silently drops unknown ones.
- **Link, don't restate:** reference related issues by number; note the PR that will resolve it. Prefer closing via a merged PR that says `Closes #123`, so the link is recorded in history.
- Add assignee/milestone when they're genuinely known; don't guess.

## Comment etiquette

- Keep the issue the source of truth. Decisions, reproductions, and changes of direction belong **in the issue**, not lost in a chat transcript.
- When your understanding changes, **edit the body** to match — a stale spec is worse than none. Use comments for the running discussion; use the body for the current truth.
- Be concise and kind; assume the reader is a busy collaborator, not a support queue.

## Closing

- **Close only when Done when is fully checked or honestly marked obsolete**, and you've verified it. Don't close with `- [ ]` still open, and don't close leftover work as a "follow-up" — finish it or mark it obsolete first.
- Closing as won't-do is fine, but say why in a comment so the record explains itself.

## Good vs bad

```
❌ Title: "save is broken"
   Body: "saving doesn't work sometimes, pls fix"

✅ Title: "fix: crash when saving a profile with no display name"
   Body: repro steps, expected vs actual, the stack trace, and
   Done when:
   - [ ] Saving an empty-name profile no longer throws
   - [ ] A regression test covers the empty-name case
```
