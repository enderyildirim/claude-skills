---
name: mediocre
description: "After a mediocre fix, scrap it and implement the elegant solution using everything learned so far"
user-invocable: true
---

# Mediocre — Redo It Right

Knowing everything you know now, scrap this and implement the elegant solution.

## How this works

You've just attempted a fix or implementation. It works, but it's not great — it's patchy, over-complicated, or just "good enough." Now that you understand the problem fully (the constraints, the edge cases, the codebase patterns), throw away the mediocre attempt and write the version you would have written if you'd known all of this from the start.

## Steps

1. **Analyze the current attempt** — Read the changes made so far. Identify what's mediocre about them: unnecessary complexity, wrong abstraction level, missed patterns, band-aid fixes, or poor fit with the surrounding code.

2. **Extract the insights** — What did you learn from the first attempt? What constraints became clear? What edge cases surfaced? What existing patterns should be leveraged?

3. **Scrap and rewrite** — Discard the mediocre solution entirely. Don't iterate on it — start fresh with the elegant approach. Use the full understanding gained from the first attempt.

4. **Verify** — Run the relevant checks (lint, test, build) to confirm the elegant solution works correctly.

## Principles

- The first attempt was the learning phase. This is the building phase.
- Simpler is almost always more elegant. Fewer lines, fewer abstractions, fewer special cases.
- Match the patterns already in the codebase — don't invent new ones.
- If the elegant solution is the same as the mediocre one, say so honestly. Not everything needs a rewrite.
