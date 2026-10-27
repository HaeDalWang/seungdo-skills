---
name: grilling
description: Interview the user in rounds until a plan has no silent assumptions — a design tree of decisions, each round asking every question answerable now, each with a recommended answer. Use when the user asks to be grilled or questioned about a plan, says 질문해줘 / 빠진 거 물어봐 / 따져봐 / 그릴, or when scoping work whose requirements are still vague (a SOW, a migration, an upgrade plan) before writing anything.
license: MIT
metadata:
  origin: mattpocock/skills
  upstream: https://github.com/mattpocock/skills/blob/95249b0/skills/productivity/grilling/SKILL.md
  upstream-license: MIT
  upstream-copyright: Copyright (c) 2026 Matt Pocock
  modified: "Facts gathered with own tools first (subagent only for wide sweeps); recommended answers carry a certainty label; ends with a decision list."
---

# Grilling

Interview the user relentlessly until you reach a shared understanding. Map the plan as a
**design tree**: every decision branches into the decisions that hang off it.

## Rounds

The **frontier** is every decision whose prerequisites are already settled — the questions
answerable *now*. Ask the whole frontier in one round, numbered, each with your
recommended answer, then wait for the answers.

```
❓ **Q1** - **<question title>**: <question body, choices if any>

➡️ <recommended answer>

---

❓ **Q2** - ...
```

- Word each question so "yes" accepts the recommendation.
- A question that depends on another question still open this round belongs to a later
  round.
- After each round, recompute the frontier: settled decisions unblock the ones below them.

## Facts vs decisions

Facts are your job: anything the environment can answer (repo, config, running system,
docs) you look up yourself with your tools before asking. Use a subagent only for a wide
sweep, and keep asking the rest of the frontier while it runs — only the questions
downstream of it wait.

Decisions are the user's: put each one to them and wait.

A recommendation that rests on a fact carries its certainty: `confirmed` (checked this
session) or `hypothesis` (with the check that settles it).

## Done

The frontier is empty: every branch visited, nothing silently assumed. Close with the
decision list — one line per settled decision — and wait for the user to confirm it
before acting on it.
