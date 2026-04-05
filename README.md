# Claude Skills & Agents

Reusable [Claude Code](https://claude.ai/code) skills and agents that work across any project. Skills are inline workflow tools; agents are isolated AI workers with their own context window.

## Skills

| Skill | Command | What it does |
|-------|---------|-------------|
| **hold** | `/hold` | Pause implementation, switch to discussion-only mode. No code changes until you say "go ahead." |
| **mediocre** | `/mediocre` | After a mediocre fix, scrap it and redo it right — using everything learned from the first attempt. |
| **research** | `/research <feature>` | Research implementation approaches with alternatives comparison, trade-off analysis, and architecture fit. Produces a structured report, no code. |
| **sidetrack** | `/sidetrack [deep] <issue>` | Evaluate a side issue found during development. Assess coupling, compare fixes, decide: fix now or defer? |
| **commit-push-pr** | `/commit-push-pr [desc]` | Commit, push, and create a PR in one step. Reads project conventions from CLAUDE.md. |
| **learn** | `/learn [topic]` | Capture lessons from the current conversation and add them to the project's CLAUDE.md. |
| **tidy-permissions** | `/tidy-permissions` | Clean up settings.json permissions — consolidate similar rules with wildcards, remove duplicates, sort alphabetically. |

## Agents

| Agent | Command | What it does |
|-------|---------|-------------|
| **issue-creator** | `/issue-creator <description>` | Investigate a problem, create a detailed GitHub issue with root cause analysis. Runs in isolated context. Supports `list`, `fix <n>`, `close <n>`, `search`, `view <n>` subcommands. |
| **code-simplifier** | `/code-simplifier` | Clean up code after implementation — remove dead code, simplify verbose patterns, consolidate duplicates. Verifies with lint/test/build. |

## Installation

```bash
# Clone the repo
git clone https://github.com/enderyildirim/claude-skills.git ~/dev/tools/claude-skills

# Symlink skills
cd ~/.claude/skills
ln -s ~/dev/tools/claude-skills/skills/hold hold
ln -s ~/dev/tools/claude-skills/skills/mediocre mediocre
ln -s ~/dev/tools/claude-skills/skills/research research
ln -s ~/dev/tools/claude-skills/skills/sidetrack sidetrack
ln -s ~/dev/tools/claude-skills/skills/commit-push-pr commit-push-pr
ln -s ~/dev/tools/claude-skills/skills/learn learn
ln -s ~/dev/tools/claude-skills/skills/tidy-permissions tidy-permissions

# Symlink agents
cd ~/.claude/agents
ln -s ~/dev/tools/claude-skills/agents/issue-creator.md issue-creator.md
ln -s ~/dev/tools/claude-skills/agents/code-simplifier.md code-simplifier.md
```

> **Note:** Project-level skills/agents override user-level ones with the same name. This lets you keep project-specific versions that add extra context.

## Updating

```bash
cd ~/dev/tools/claude-skills && git pull
```

Symlinks mean updates are immediately available.

## License

MIT
