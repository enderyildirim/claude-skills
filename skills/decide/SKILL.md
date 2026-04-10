---
name: decide
description: "Guided decision flow using the interactive AskUserQuestion UI — structured options the user clicks to answer, not text walls. Usage: /decide [topic]"
user-invocable: true
---

# Decide — Guided Decision Flow

Turn any multi-question decision process into an interactive, click-to-answer experience using the **AskUserQuestion** tool. Instead of writing numbered lists or markdown tables for the user to read through, you present questions in Claude Code's native selection UI.

## How It Works

The user invokes `/decide [topic]`. You then:

1. Silently identify all decision points for the topic
2. Prioritize them — foundational decisions first, then dependent ones
3. Present questions using the **AskUserQuestion** tool (1-4 per batch)
4. Read the user's selections, adapt remaining questions accordingly
5. Repeat until all decisions are made
6. Present a final summary

---

## Using AskUserQuestion

**ALWAYS use the AskUserQuestion tool to present choices.** Never fall back to markdown tables or numbered lists.

### Batching Strategy

AskUserQuestion supports 1-4 questions per call. Use this wisely:

- **Independent questions** — batch up to 4 together (e.g., food theme + drink choice + music genre)
- **Dependent questions** — ask in separate batches (e.g., ask "indoor or outdoor?" first, then location-specific questions in the next batch)
- **Simple topics** (3-4 total questions) — can often be done in 1-2 batches
- **Complex topics** (6-10 total questions) — use 3-4 batches, grouping related decisions

### Question Structure

Each question in the AskUserQuestion call requires:

- **`question`** — Clear, specific question ending with `?`
- **`header`** — Short label, max 12 chars (e.g., "Database", "Auth", "Deploy")
- **`options`** — 2-4 choices, each with a `label` and `description`
- **`multiSelect`** — `true` if multiple options can be combined, `false` for mutually exclusive choices

### Recommendation Pattern

When you have a recommendation, put it as the **first option** and append `(Recommended)` to the label:

```json
{
  "question": "Which database should we use?",
  "header": "Database",
  "options": [
    {"label": "PostgreSQL (Recommended)", "description": "Full-featured, JSON support, extensions ecosystem"},
    {"label": "SQLite", "description": "Zero config, embedded, great for small scale"},
    {"label": "MySQL", "description": "Battle-tested, wide hosting support"}
  ],
  "multiSelect": false
}
```

### Preview Feature

Use the `preview` field on options when the user needs to **visually compare** concrete artifacts:

- ASCII mockups of UI layouts
- Code snippets showing different implementations
- Configuration examples
- Architecture diagrams

```json
{
  "question": "Which layout for the dashboard?",
  "header": "Layout",
  "options": [
    {
      "label": "Sidebar nav (Recommended)",
      "description": "Fixed sidebar with collapsible sections",
      "preview": "┌──────┬─────────────────┐\n│ Nav  │                 │\n│      │    Content       │\n│ Home │                 │\n│ Data │                 │\n│ Sets │                 │\n└──────┴─────────────────┘"
    },
    {
      "label": "Top nav",
      "description": "Horizontal navbar with dropdown menus",
      "preview": "┌─────────────────────────┐\n│  Home  Data  Settings   │\n├─────────────────────────┤\n│                         │\n│       Content           │\n│                         │\n└─────────────────────────┘"
    }
  ],
  "multiSelect": false
}
```

Previews trigger a side-by-side layout in the UI — option list on the left, preview on the right. Only use for single-select questions.

---

## Flow Control

### Starting the flow

After `/decide [topic]`:

1. **Silently** map out all decision points
2. **Silently** determine batching (which questions are independent, which depend on prior answers)
3. Optionally write one short context line before the first AskUserQuestion call
4. Call AskUserQuestion with the first batch

### Between batches

After the user answers:

1. **Acknowledge briefly** — one short line summarizing what was chosen
2. If answers change the remaining questions, adapt silently
3. Call AskUserQuestion with the next batch

### Ending the flow

After all questions are answered, present a **Decision Summary**:

```
## Decisions Made

| Decision | Choice |
|----------|--------|
| Database | PostgreSQL |
| ORM | Prisma |
| Auth | Session-based |
| Deploy | Railway |

Ready to proceed, or want to revisit any of these?
```

---

## Adaptive Behavior

- **If the user selects "Other"** with custom text — respect it, incorporate into the flow
- **If the user wants to go back** — re-present the previous batch via AskUserQuestion
- **If context changes mid-flow** — adapt remaining questions, don't re-ask settled decisions unless now invalid
- **If the topic only has 1-2 decisions** — single AskUserQuestion call, no need for multiple rounds

---

## Rules

- **Always use AskUserQuestion** — never markdown tables, never numbered lists, never inline text options
- **Be opinionated** — put your recommendation first with `(Recommended)` suffix
- **Use previews for visual decisions** — layouts, code patterns, UI mockups
- **Keep descriptions short** — one line per option description, no paragraphs
- **Batch smartly** — independent questions together, dependent ones in separate calls
- **No code changes** — this skill only makes decisions, it doesn't implement them
- **Respect previous answers** — each subsequent batch should factor in what was already decided
