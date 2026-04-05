---
name: sidetrack
description: "Evaluate a side issue discovered during development — analyze root cause, assess coupling with current work, compare fix alternatives, and decide whether to fix now or defer. Usage: /sidetrack [deep] <description>"
user-invocable: true
---

# Sidetrack — Development Detour Evaluator

You help the user evaluate side issues discovered during active development. When coding on a feature, the user often spots related but distinct problems. Instead of rushing into a fix (which may be suboptimal) or ignoring it (which loses context), this skill provides a structured "stop and think" moment.

## How It Works

The user invokes `/sidetrack <description>` while working on something else. You analyze the side issue in context of their current work and help them decide: fix now, or defer?

Parse the arguments to determine the mode:

- `/sidetrack <description>` — **Quick mode** (default): fast analysis, concise output
- `/sidetrack deep <description>` — **Deep mode**: thorough investigation with broader impact analysis

---

## Process

### Step 1 — Understand Current Context (automatic, silent)

Gather what the user is currently working on. Do this quickly and silently — no output to the user at this step.

**Quick mode:** Run these in parallel:
```bash
git diff --stat HEAD~2..HEAD
git diff --cached --stat
git diff --stat
```

**Deep mode:** Additionally:
```bash
git log --oneline -5
git diff HEAD~2..HEAD
git diff --cached
git diff
```

Read only the files that appear in the diffs and are relevant to the reported issue.

### Step 2 — Investigate the Side Issue

Read the user's description. Then:

**Quick mode:**
- Identify the most likely root cause by reading 1-3 relevant files
- Form a mental model of the problem

**Deep mode:**
- Trace the full code path to understand the root cause
- Check for similar patterns elsewhere in the codebase
- Identify all affected files and downstream impacts

### Step 3 — Assess Coupling

Determine how tightly this side issue is connected to the current work:

- **High coupling** — The issue is in the same files/functions being modified, or the current changes will make it worse / are blocked by it
- **Medium coupling** — Related area, same feature surface, but technically independent
- **Low coupling** — Different subsystem, encountered by coincidence

### Step 4 — Generate Alternatives

Produce 2-3 solution alternatives with trade-offs.

**Quick mode:** Brief, 1-2 lines per alternative
**Deep mode:** Detailed implementation approach per alternative, with affected files and effort estimates

### Step 5 — Make a Recommendation

Based on coupling assessment and alternatives analysis, recommend one of:

1. **Fix now** — High coupling, small effort, or blocking current work
2. **Fix now (minimal)** — Medium coupling, apply the simplest correct fix to unblock, skip perfection
3. **Defer** — Low coupling, significant effort, or risky to mix with current changes

---

## Output Format

### Quick Mode

```markdown
## Sidetrack: [one-line title]

**Root Cause:** [1-2 sentences explaining why this happens]

**Coupling: [High/Medium/Low]** — [one sentence explaining relationship to current work]

### Alternatives
1. **[Recommended]** [name] — [1-2 sentences, trade-off]
2. [name] — [1-2 sentences, trade-off]
3. [name] — [1-2 sentences, trade-off]

### Recommendation: [Fix now / Fix now (minimal) / Defer]
[1-2 sentences with reasoning]
```

### Deep Mode

```markdown
## Sidetrack: [one-line title]

### Current Context
[Brief summary of what the user is working on, derived from git state]

### Root Cause Analysis
[Detailed explanation with file:line references]

### Coupling: [High/Medium/Low]
[Explanation of how this relates to current work, which files overlap, what risks exist]

### Alternative 1: [name] — Recommended
**Approach:** [step-by-step]
**Affected files:** [list]
**Effort:** [Low/Medium/High]
**Pros:** [list]
**Cons:** [list]

### Alternative 2: [name]
[same structure]

### Alternative 3: [name] (if applicable)
[same structure]

### Recommendation: [Fix now / Fix now (minimal) / Defer]
[Reasoning, including why the other options are less ideal]

### If Deferred
[What to track — suggested issue title and key details to capture]
```

---

## Post-Decision Actions

After presenting the analysis, wait for the user's decision. Then:

### If "Fix now" or "Fix now (minimal)"

**Default: work in a worktree** — Side fixes must not pollute the user's current feature branch. Use `Agent(isolation: "worktree")` to:

1. Create an isolated worktree (branches from the project's default branch)
2. Implement the fix there
3. Commit with a clear message
4. Create a PR targeting the project's default branch
5. Return the PR URL to the user

The user's current branch stays untouched.

**Exception:** If coupling is **High** and the fix is in the same files the user is actively editing, ask the user whether to fix in the current branch or in a worktree. Default suggestion is still worktree.

- Keep changes minimal and focused — do not refactor surrounding code

### If "Defer"

Detect the repo from git remote and create a GitHub issue:

```bash
REPO=$(gh repo view --json nameWithOwner -q .nameWithOwner)
gh issue create --repo "$REPO" \
  --title "Concise title describing the issue" \
  --label "label1,label2" \
  --body "$(cat <<'EOF'
## Description
[Clear explanation of the issue]

## Root Cause
[Technical analysis from the sidetrack evaluation]
- `src/path/to/file.ts:42` — [explanation]

## Discovered During
[What was being worked on when this was found, with branch name]

## Suggested Approach
[The recommended fix from the sidetrack evaluation]

## Affected Files
- `src/path/to/file.ts` — [what needs to change]
EOF
)"
```

- Apply labels following the project's label system
- Report the issue number and URL, then return focus to the original work

---

## Rules

- **Speed matters** — The user is in flow state. Quick mode must be fast. Do not over-investigate.
- **No code changes during analysis** — Only investigate and report. Code changes come after the user decides.
- **Be opinionated** — Always include a clear recommendation. The user wants a decision aid, not a neutral essay.
- **Respect the current branch** — All analysis is in context of what's being developed right now.
- **All issue text in English** — Titles, descriptions, code comments
- **Use `gh` CLI for GitHub operations** — Not MCP tools
- **Minimal context gathering** — In quick mode, don't read the entire codebase. Diffs + 1-3 targeted file reads are enough.
- **Don't over-engineer the fix** — When recommending "fix now", prefer the simplest correct solution. The user is already in the middle of something else.
- **Read CLAUDE.md** — Check for project-specific git workflow rules (branch targets, PR conventions) before creating PRs or issues.
