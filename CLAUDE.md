# Claude Skills & Agents

Reusable Claude Code skills and agents — workflow meta-tools that work across any project. Packaged as a Claude Code **plugin** named `flow`, distributed via this repo's own marketplace.

## Repo Structure

```
.claude-plugin/
  plugin.json     # Plugin manifest (name: flow)
  marketplace.json # Marketplace manifest (name: claude-skills) — lists the flow plugin, source "."
skills/           # Inline workflow tools (run in main conversation context)
  discuss/        # Pause implementation, discussion-only mode
  mediocre/       # Scrap mediocre fix, redo it right
  research/       # Research implementation approaches, produce report
  sidetrack/      # Evaluate side issues: fix now or defer?
  worktree/       # Isolate parallel tasks in separate git worktrees
  commit-push-pr/ # Commit, push, PR in one step
  decide/         # Guided decision flow via AskUserQuestion UI
  learn/          # Capture conversation lessons into CLAUDE.md
  tidy-permissions/ # Clean up settings.json permission rules
agents/           # Isolated AI workers (own context window)
  issue-creator.md    # Investigate & create GitHub issues
  code-simplifier.md  # Clean up code after implementation
```

The repo root **is** the `flow` plugin (marked by `.claude-plugin/plugin.json`), and it also serves itself as a single-plugin marketplace (`.claude-plugin/marketplace.json`, plugin `source: "."`). Once installed, skills resolve as `/flow:<name>`.

## Conventions

- **Skills** are directories with a `SKILL.md` file (YAML frontmatter + markdown body)
- **Agents** are single `.md` files (YAML frontmatter + markdown body)
- All skills/agents must be **project-agnostic** — no hardcoded repo names, paths, or tech stacks
- Skills/agents should read `CLAUDE.md` of the target project for project-specific conventions
- Dynamic repo detection: use `gh repo view --json nameWithOwner -q .nameWithOwner`
- Dynamic branch detection: read project's CLAUDE.md for PR base branch conventions
- All content in English
- File names use kebab-case

## How Users Install

Users add the marketplace and install the plugin from any Claude Code session (one time per machine):

```
/plugin marketplace add enderyildirim/claude-skills
/plugin install flow@claude-skills
```

Updates: `/plugin marketplace update claude-skills` then `/plugin update flow@claude-skills` (or the `claude plugin ...` terminal equivalents). Adding a skill/agent here and pushing means every machine picks it up on the next update.

Project-level skills/agents (in a repo's `.claude/`) override plugin ones with the same name. The older symlink-into-`~/.claude/skills/` method is deprecated — see README "Migrating from symlinks" for cleanup.

## Adding New Skills or Agents

### Skill vs Agent — When to Use Which

- **Skill** if it needs the current conversation's context (corrections, recent discussion, inline workflow)
- **Agent** if it does heavy independent work (codebase investigation, large refactors, background tasks)

### Skill Template

```markdown
---
name: skill-name
description: "One-line description. Usage: /skill-name [args]"
user-invocable: true
---

# Skill Title

What this skill does in one paragraph.

## Process
[Step-by-step instructions]

## Rules
[Constraints and guardrails]
```

### Agent Template

```markdown
---
name: agent-name
description: "When to invoke this agent — include trigger phrases users might say"
user-invocable: true
model: opus
effort: high
maxTurns: 25
disallowedTools: [tools agent should NOT use]
---

# Agent Title

What this agent does in one paragraph.

## Process
[Step-by-step instructions]

## Rules
[Constraints and guardrails]
```

## Key Principles

- **No hardcoded project references** — skills must work in any repo
- **Read CLAUDE.md first** — every skill/agent should check the target project's conventions
- **Be opinionated** — always include clear recommendations, not neutral essays
- **Show before applying** — destructive or config changes need user approval first
- **Keep it fast** — these are workflow accelerators, not deep audits
