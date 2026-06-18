# Claude Skills & Agents

Reusable [Claude Code](https://claude.ai/code) skills and agents that work across any project. Skills are inline workflow tools; agents are isolated AI workers with their own context window.

Distributed as a Claude Code **plugin** named `flow` via this repo's built-in marketplace. Once installed, every machine you use stays in sync — `git pull` upstream and `/plugin update` pulls the latest.

## Skills

| Skill | Command | What it does |
|-------|---------|-------------|
| **discuss** | `/flow:discuss` | Pause implementation, switch to discussion-only mode. No code changes until you say "go ahead." |
| **research** | `/flow:research <feature>` | Research implementation approaches with alternatives comparison, trade-off analysis, and architecture fit. Produces a structured report, no code. |
| **sidetrack** | `/flow:sidetrack [deep] <issue>` | Evaluate a side issue found during development. Assess coupling, compare fixes, decide: fix now or defer? |
| **worktree** | `/flow:worktree [create\|list\|remove\|clean]` | Isolate parallel, independent tasks in separate git worktrees so they never overwrite each other — each task gets its own working directory and branch. |
| **mediocre** | `/flow:mediocre` | After a mediocre fix, scrap it and redo it right — using everything learned from the first attempt. |
| **commit-push-pr** | `/flow:commit-push-pr [desc]` | Commit, push, and create a PR in one step. Reads project conventions from CLAUDE.md. |
| **decide** | `/flow:decide [topic]` | Guided decision flow using the interactive AskUserQuestion UI — structured options you click, not text walls. |
| **learn** | `/flow:learn [topic]` | Capture lessons from the current conversation and add them to the project's CLAUDE.md. |
| **tidy-permissions** | `/flow:tidy-permissions` | Clean up settings.json permissions — consolidate similar rules with wildcards, remove duplicates, sort alphabetically. |

## Agents

| Agent | Command | What it does |
|-------|---------|-------------|
| **issue-creator** | `/flow:issue-creator <description>` | Investigate a problem, create a detailed GitHub issue with root cause analysis. Runs in isolated context. Supports `list`, `fix <n>`, `close <n>`, `search`, `view <n>` subcommands. |
| **code-simplifier** | `/flow:code-simplifier` | Clean up code after implementation — remove dead code, simplify verbose patterns, consolidate duplicates. Verifies with lint/test/build. |

## Installation

Install once per machine, from inside any Claude Code session:

```
/plugin marketplace add enderyildirim/claude-skills
/plugin install flow@claude-skills
```

Restart Claude Code (or start a new session) and the skills/agents are available as `/flow:<name>`.

> Prefer the terminal? The same thing non-interactively:
> ```bash
> claude plugin marketplace add enderyildirim/claude-skills
> claude plugin install flow@claude-skills
> ```

## Updating

After pushing changes to this repo:

```
/plugin marketplace update claude-skills
/plugin update flow@claude-skills
```

Or from the terminal: `claude plugin update flow@claude-skills`. Restart to apply. Because the plugin is sourced from GitHub, every machine that has it installed picks up the update the same way.

## Migrating from symlinks

Earlier these skills were linked into `~/.claude/skills/` and `~/.claude/agents/` with `ln -s`. After installing the plugin, remove the old symlinks so they don't shadow the plugin versions:

```bash
# Skills (only delete the ones that are symlinks into this repo)
for s in commit-push-pr decide discuss hold learn mediocre research sidetrack tidy-permissions; do
  [ -L ~/.claude/skills/$s ] && rm ~/.claude/skills/$s
done
# Agents
for a in code-simplifier.md issue-creator.md; do
  [ -L ~/.claude/agents/$a ] && rm ~/.claude/agents/$a
done
```

> **Note:** Project-level skills/agents (in a repo's `.claude/`) still override plugin ones with the same name — handy for project-specific variants.

## License

MIT
