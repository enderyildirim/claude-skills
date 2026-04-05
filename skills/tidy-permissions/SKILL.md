---
name: tidy-permissions
description: "Clean up and optimize Claude Code permission rules in settings.json — consolidate similar rules with wildcards, remove duplicates, sort alphabetically. Usage: /tidy-permissions"
user-invocable: true
---

# Tidy Permissions

You clean up and optimize permission rules in Claude Code settings.json files. When invoked, you read all permission configs, consolidate similar rules, remove duplicates, and produce a clean, organized permission list.

## How It Works

The user invokes `/tidy-permissions`. You then analyze all settings files and optimize the permission rules.

## Process

### Step 1 — Read All Settings Files

Read these files (silently, skip if missing):

```
~/.claude/settings.json                          # user-level
.claude/settings.json                            # project-level (committed)
.claude/settings.local.json                      # project-level (local)
```

Extract the `permissions.allow`, `permissions.deny`, and `permissions.ask` arrays from each.

### Step 2 — Analyze & Consolidate

For each permission array (`allow`, `deny`, `ask`), apply these optimizations:

**Remove exact duplicates:**
- `Bash(npm run test)` appearing twice → keep one

**Consolidate with wildcards:**
Look for groups of 3+ rules that share a common prefix:
- `Bash(npm run lint)`, `Bash(npm run test)`, `Bash(npm run build)` → `Bash(npm:*)`
- `Bash(git status)`, `Bash(git diff)`, `Bash(git log)`, `Bash(git add)` → `Bash(git:*)`
- `Bash(cd ~/dev)`, `Bash(cd ~/projects)` → keep separate (only 2, not worth consolidating)

**Be conservative with wildcards:**
- Only consolidate when ALL rules in the group are safe to wildcard
- `Bash(git status)` + `Bash(git push --force)` should NOT become `Bash(git:*)` — force push is dangerous
- When in doubt, keep rules separate
- Never consolidate rules across different tools (Edit + Write)

**Common safe consolidation patterns:**
| Rules | Consolidation |
|-------|--------------|
| `Bash(npm run *)` variants | `Bash(npm:*)` |
| `Bash(git status)`, `Bash(git diff*)`, `Bash(git log*)`, `Bash(git branch*)` | `Bash(git:*)` (only if no destructive git commands are in the list) |
| `Bash(ls *)` variants | `Bash(ls:*)` |
| `Bash(npx *)` variants | `Bash(npx:*)` |
| `Bash(node *)` variants | `Bash(node:*)` |
| `Bash(cd *)` variants | `Bash(cd:*)` |
| Multiple `Edit(.claude/*)` | `Edit(.claude)` |
| Multiple `Read(src/*)` | `Read` (if covering most of src) |

**Dangerous patterns — do NOT auto-consolidate:**
- Any `git push`, `git reset`, `git checkout --`, `git clean` → keep explicit
- Any `rm`, `rmdir`, `kill` → keep explicit
- Any `sudo` commands → keep explicit

### Step 3 — Sort & Organize

Sort each array:
1. **By tool name** alphabetically (Bash, Edit, Glob, Grep, Read, Write)
2. **Within each tool**, alphabetically by the rule content
3. **Wildcard rules first** within each tool group (they're the broadest)

### Step 4 — Present Changes

Show the user a before/after comparison for each file that would change:

```
## ~/.claude/settings.json — permissions.allow

Before (12 rules):
  Bash(cd ~/dev/projects/neonloops)
  Bash(git add)
  Bash(git branch)
  Bash(git diff)
  Bash(git log)
  Bash(git status)
  Bash(npm run build)
  Bash(npm run lint)
  Bash(npm run test)
  Edit(.claude/settings.local.json)
  Edit(.claude/skills)
  Read

After (5 rules):
  Bash(cd:*)
  Bash(git:*)
  Bash(npm:*)
  Edit(.claude)
  Read

Removed: 7 redundant rules
```

Wait for the user's approval. If they object to any consolidation, keep those rules separate.

### Step 5 — Apply

After approval, edit the settings files with the optimized permission arrays.

### Step 6 — Verify

```bash
jq -e '.permissions' <target-file>
```

Confirm the JSON is valid after editing.

## Rules

- **Never consolidate dangerous commands** — rm, kill, sudo, force push stay explicit
- **Always show before/after** — never edit without approval
- **Preserve deny rules carefully** — consolidating deny rules is risky, be extra conservative
- **Don't merge across files** — user, project, and local settings stay separate
- **Don't add new permissions** — only reorganize existing ones
- **Don't remove permissions** — only consolidate or deduplicate
- **Validate JSON after editing** — broken settings.json silently disables all settings
- **Keep it fast** — this is a quick cleanup, not a security audit
