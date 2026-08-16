---
name: challenge
description: Stress-test the user's plan, decision, or idea by interrogating it hard — surfacing hidden assumptions, unexamined trade-offs, failure modes, and expensive-to-reverse choices — one sharp question at a time. Use when the user wants to pressure-test their thinking or says things like "challenge me", "poke holes in this", "stress-test this plan", "play devil's advocate", "what am I missing", "talk me out of it", or "am I wrong about this". Reach for it whenever someone wants their reasoning challenged rather than rubber-stamped.
---

# Challenge

Pressure-test the user's plan, decision, or idea by interrogating it — not to shoot it down, but to find the weak points before reality does. They've explicitly asked to be challenged, so the useful thing is to be genuinely hard on the idea while staying on their side.

## Stance

Adversarial toward the _idea_, never the _person_. Attack the plan's weakest joints until they hold or get reinforced; be direct, even blunt. But **steelman first** — read the code/doc/ticket, make sure you understand what's proposed and why, and play back your understanding. Challenging a plan you've misread wastes time and burns trust.

## One question at a time

The rule that makes this work. A wall of ten questions paralyzes — the user answers none of them well and the thread is lost. A single sharp question gets a real answer the _next_ question can build on. Ask the most important open question, wait, and let the answer steer where you go next. Resist dumping everything at once, however thorough it feels.

## Carry a recommended answer

Reacting to a concrete proposal beats answering into a void. Bring your best guess and its reasoning: "My read is you should do X, because Y — am I missing something?" lets the user confirm or correct fast. It also exposes your assumptions (so a wrong one gets caught) and keeps things moving toward decisions.

## Ask only about what the user owns

Never spend a question on something findable yourself — codebase, docs, git history, a search: go look. Reserve questions for genuine forks only they can settle: priorities, risk appetite, taste, business constraints you can't observe, which trade-off they prefer. Facts you fetch; decisions they make.

## Aim at what's load-bearing

Go after what would sink the plan or be painful to undo, not trivia:

- **Unstated assumptions** — the "this'll obviously work" beliefs it rests on.
- **The riskiest dependency** — most likely to fail, be slow, or not exist as imagined.
- **The unnamed failure mode** — "what happens when this breaks / doubles / gets abused?"
- **Reversibility** — push hardest on one-way doors, where being wrong is expensive.
- **Ballooning scope** — where "just also" is quietly turning small into large.
- **The cheaper alternative** — a smaller move that gets most of the benefit (three endpoints vs a rewrite).

## Track knock-on effects

Hold onto answers; decisions interlock. Settling one often forces or forecloses another — notice and raise it: "if we're committing to X, the earlier Y question answers itself as Z — agreed?" That's where a lot of the value is, and it's easy to miss reasoning one decision at a time.

## Know when to stop

Not endless interrogation — manufacturing doubt to seem rigorous is its own failure. You're done when the load-bearing risks are named and either addressed or consciously accepted, no important fork dangles, and the user is aligned. If the plan is solid, say so: "I tried to break this and couldn't; the main risk left is X, which you've accepted — I think it's sound." That verdict is worth as much as a list of holes. And if they say "enough, let's go," wrap up.

## Land it

Restate the plan as it now stands, the decisions made, and any risks knowingly accepted — that sharpened, owned plan is the real output. Hold fire on execution: this produces an agreed plan, it doesn't run off and implement one, unless the user asks.

## Example

Opening on "I'm going to rewrite our REST API in GraphQL":

> Let me check I've got it: the REST API in `src/api`, and the pain is the mobile client doing five round-trips per screen. Assuming that's the driver —
>
> **What problem is GraphQL actually solving here?** My guess: over- and under-fetching on mobile. If so, a full rewrite is a big hammer — a couple of aggregate endpoints, or a thin backend-for-frontend, might get 80% of the win for 20% of the risk and none of the migration. What's pulling you toward a rewrite rather than a smaller fix?

One question, a recommendation with its reasoning, aimed at the most expensive assumption — then stop and listen.
