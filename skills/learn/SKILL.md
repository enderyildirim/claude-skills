---
name: learn
description: "Capture lessons learned from the current conversation and add them to the project's CLAUDE.md. Usage: /learn [topic]"
user-invocable: true
---

# Learn — Capture Knowledge into CLAUDE.md

You extract lessons learned from the current conversation and persist them into the project's CLAUDE.md file so that future sessions benefit.

## How It Works

The user invokes `/learn [optional topic]` after discovering something important — a gotcha, a pattern, a convention, a debugging insight. You analyze the conversation, extract the key learnings, and append them to CLAUDE.md in the right section.

If a topic is provided, focus on that area. If not, scan the entire conversation for learnable moments.

## Process

### Step 1 — Identify Learnings

Review the current conversation for:

- **Corrections the user made** — "no, don't do it that way" moments
- **Surprising behaviors** — things that weren't obvious from reading the code
- **Discovered conventions** — patterns that should be followed going forward
- **Gotchas and pitfalls** — mistakes that future sessions should avoid
- **Architecture decisions** — why something was done a certain way
- **Tool/library quirks** — non-obvious behavior of dependencies

### Step 2 — Read Current CLAUDE.md

Read the project's CLAUDE.md to:
- Understand the existing structure and sections
- Check if the learning is already documented (avoid duplicates)
- Find the right section to add the new knowledge

### Step 3 — Draft the Addition

Write the learning in the style and format of the existing CLAUDE.md:
- Match the tone (terse rules vs detailed explanations)
- Match the formatting (bullet points, tables, code blocks)
- Keep it concise — future Claude sessions will read this every time
- Focus on actionable rules, not narrative

### Step 4 — Present to User

Show the user what you want to add and where:

```
I'd add this to the [Section Name] section of CLAUDE.md:

> - **Rule/Convention:** Explanation of what to do or avoid
```

Wait for the user's approval before editing.

### Step 5 — Apply

After approval, edit CLAUDE.md with the addition.

## What Makes a Good CLAUDE.md Entry

**Good entries are:**
- Actionable — tells future Claude what to DO or NOT DO
- Concise — one rule, one line if possible
- Non-obvious — wouldn't be inferred from reading the code
- Durable — will be true for a long time, not ephemeral

**Bad entries (skip these):**
- Things already in the code (function names, file paths that can be found by reading)
- Temporary state ("currently working on X")
- Things that are obvious from the framework/language

## Rules

- **Never edit CLAUDE.md without showing the user first** — always present the draft addition
- **Don't duplicate** — if the learning is already documented, say so
- **Keep it minimal** — add the smallest useful addition, not a paragraph
- **Match existing style** — read CLAUDE.md first and follow its conventions
- **All text in English** — CLAUDE.md content must be in English
