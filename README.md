# Claude Skills

Reusable [Claude Code](https://claude.ai/code) skills — workflow meta-skills that work across any project. These are "thinking tools" that shape how Claude approaches work, not project-specific utilities.

## Skills

| Skill | Command | What it does |
|-------|---------|-------------|
| **hold** | `/hold` | Pause implementation, switch to discussion-only mode. No code changes until you say "go ahead." |
| **mediocre** | `/mediocre` | After a mediocre fix, scrap it and redo it right — using everything learned from the first attempt. |
| **research** | `/research <feature>` | Research implementation approaches with alternatives comparison, trade-off analysis, and architecture fit. Produces a structured report, no code. |
| **sidetrack** | `/sidetrack [deep] <issue>` | Evaluate a side issue found during development. Assess coupling, compare fixes, decide: fix now or defer? Quick mode for fast analysis, deep mode for thorough investigation. |
| **commit-push-pr** | `/commit-push-pr [desc]` | Commit, push, and create a PR in one step. Reads project conventions from CLAUDE.md for base branch and commit style. |
| **issue** | `/issue <description>` | Investigate a problem, create a detailed GitHub issue with root cause analysis and affected files. Supports `list`, `fix <n>`, `close <n>`, `search`, `view <n>` subcommands. |

## Installation

```bash
# Clone the repo
git clone https://github.com/enderyildirim/claude-skills.git ~/dev/tools/claude-skills

# Symlink each skill into your user-level Claude skills directory
cd ~/.claude/skills
ln -s ~/dev/tools/claude-skills/hold hold
ln -s ~/dev/tools/claude-skills/mediocre mediocre
ln -s ~/dev/tools/claude-skills/research research
ln -s ~/dev/tools/claude-skills/sidetrack sidetrack
ln -s ~/dev/tools/claude-skills/commit-push-pr commit-push-pr
ln -s ~/dev/tools/claude-skills/issue issue
```

Skills are now available in every project via their slash commands.

> **Note:** Project-level skills (in `.claude/skills/`) override user-level skills with the same name. This lets you keep project-specific versions that add extra context.

## Updating

```bash
cd ~/dev/tools/claude-skills && git pull
```

Symlinks mean updates are immediately available.

## License

MIT
