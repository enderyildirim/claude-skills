---
name: commit-push-pr
description: "Commit staged changes, push to remote, and create a pull request — all in one step. Usage: /commit-push-pr [description]"
user-invocable: true
---

# Commit, Push & PR

One command to go from local changes to a pull request.

## How It Works

The user invokes `/commit-push-pr [optional description]`. You then:

1. Review all changes
2. Create a well-crafted commit
3. Push to remote
4. Open a pull request

If the user provides a description, use it to guide the commit message and PR body. If not, derive everything from the diff.

---

## Process

### Step 1 — Review Changes

Run these in parallel:

```bash
git status
git diff --cached --stat
git diff --stat
git log --oneline -5
git branch --show-current
```

Understand:
- What branch you're on
- What's staged vs unstaged
- Recent commit style for message consistency

If there are unstaged changes that look intentional (not build artifacts), stage them. Ask the user if unclear.

### Step 2 — Determine Base Branch

Read the project's CLAUDE.md for PR conventions (e.g., "PRs target develop" or "PRs target main"). If no convention is documented, default to `main`.

```bash
git log --oneline main..HEAD 2>/dev/null || git log --oneline origin/main..HEAD 2>/dev/null
```

Review ALL commits that will be in the PR, not just the latest.

### Step 3 — Commit

If there are uncommitted changes to include:

- Analyze all changes (staged + newly added)
- Draft a concise commit message: 1-2 sentences focusing on the "why"
- Follow the repo's existing commit style (conventional commits, etc.)
- Do not commit files that likely contain secrets (.env, credentials)

```bash
git add <relevant files>
git commit -m "$(cat <<'EOF'
<commit message>

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"
```

If everything is already committed, skip to Step 4.

### Step 4 — Push

```bash
git push -u origin HEAD
```

If the branch doesn't exist on remote yet, this creates it.

### Step 5 — Create PR

Analyze ALL commits between base branch and HEAD to write the PR:

```bash
gh pr create --title "<short title, under 70 chars>" --body "$(cat <<'EOF'
## Summary
<1-3 bullet points covering all commits in this PR>

## Test plan
- [ ] <verification steps>

🤖 Generated with [Claude Code](https://claude.ai/code)
EOF
)"
```

Use the base branch determined in Step 2 (e.g., `--base develop`).

### Step 6 — Report

Show the PR URL. Keep it brief.

---

## Rules

- **Read CLAUDE.md first** — check for PR conventions (base branch, commit style, CI requirements)
- **All PR text in English** — titles, descriptions, commit messages
- **Don't skip the review** — always look at the full diff before committing
- **Never force push** — if push fails, diagnose and tell the user
- **Never commit secrets** — skip .env, credentials, API keys
- **Use `gh` CLI** — not MCP GitHub tools
- **Keep it fast** — this is a workflow accelerator, not a code review
