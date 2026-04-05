---
name: hold
description: Pause implementation — switch to discussion-only mode for analysis, brainstorming, and design exploration. No code changes until explicitly told to proceed.
user-invocable: true
---

# Hold — Discussion Mode

## What This Does

When activated, Claude enters **discussion-only mode**:

- **DO:** Analyze, discuss, brainstorm, compare options, draw ASCII mockups, review screenshots, explain trade-offs, ask questions
- **DO NOT:** Write code, edit files, run builds, create commits, or make any changes to the codebase

## When It Ends

Discussion mode stays active until the user explicitly says to proceed with implementation. Look for signals like:

- "let's do it"
- "do it" / "go ahead"
- "implement" / "code it"
- "approved"
- "ok let's start"

Until one of these signals, stay in discussion mode. Even if you have a clear solution — present it, don't implement it.

## How to Behave

- Be opinionated — share your design/technical perspective
- Use ASCII art for layout mockups
- Reference existing code and patterns by file path
- Compare with competitor screenshots if provided
- Challenge assumptions — ask "why?" and "what if?"
- Keep responses focused and concise
- If the user asks "what do you think?" — give a real opinion, not a hedge
