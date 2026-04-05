---
name: issue
description: "Investigate a problem, create a detailed GitHub issue with root cause analysis, and optionally auto-fix it. Usage: /issue <description>"
user-invocable: true
---

# GitHub Issue Manager

You manage GitHub Issues for the current project. The user reports bugs, describes features, or mentions improvements — your job is to investigate the codebase, understand the problem deeply, and create detailed, professional GitHub issues.

## How It Works

The user invokes `/issue <description>` where description is a brief explanation of a bug, feature, or improvement. Parse the arguments to determine the action.

Detect the repo automatically:
```bash
REPO=$(gh repo view --json nameWithOwner -q .nameWithOwner)
```

---

## Actions

### `/issue <description>` — Investigate & Create Issue

This is the primary action. The user describes something they found (bug, missing feature, improvement idea).

**Step 1 — Understand the report:**
Read the user's description carefully. They may describe it casually or briefly. Extract the core problem or request.

**Step 2 — Investigate the codebase:**
- Find the relevant files, components, and code paths
- For bugs: trace the code flow to understand WHY the bug happens, identify the root cause
- For features: understand the current state and what would need to change
- Note exact file paths and line numbers

**Step 3 — Check for duplicates:**
```bash
gh issue list --repo "$REPO" --search "<relevant keywords>" --state open --json number,title
```
If a duplicate exists, tell the user and show the existing issue number.

**Step 4 — Create the issue:**
```bash
gh issue create --repo "$REPO" \
  --title "Concise, descriptive title" \
  --label "label1,label2" \
  --body "$(cat <<'EOF'
## Description
Clear explanation of the bug or feature request. Written so that anyone (including future-you) can understand the problem without additional context.

## Current Behavior
What happens now. (For bugs — describe the broken behavior. For features — describe what's missing.)

## Expected Behavior
What should happen instead.

## Root Cause
(For bugs only) Technical analysis of WHY this happens, with code references.
- `src/path/to/file.ts:42` — explanation of the problematic code

## Affected Files
- `src/path/to/file.ts` — what needs to change here
- `src/path/to/other.ts` — what needs to change here

## Suggested Approach
Concrete steps or implementation ideas to fix/build this.

## Additional Context
Any relevant details: edge cases, related functionality, screenshots the user mentioned, etc.
EOF
)"
```

**Step 5 — Ask about auto-fix:**
After creating the issue, ask the user if they want to add an `auto-fix` label. If the project has a Claude Code GitHub Action configured, this triggers automatic implementation.

If the user says yes:
```bash
gh issue edit <number> --repo "$REPO" --add-label "auto-fix"
```

**Step 6 — Report back:**
Show the user the created issue number and URL. Keep it brief.

### `/issue list` — Show Open Issues
```bash
gh issue list --repo "$REPO" --state open --json number,title,labels --limit 30
```
Display as a clean grouped table by type (bugs, features, tech-debt, other) with issue numbers.

### `/issue close <number>` — Close Issue
```bash
gh issue close <number> --repo "$REPO" --comment "Closed: <reason>"
```

### `/issue fix <number>` — Read Issue & Implement Fix
1. Read the issue: `gh issue view <number> --repo "$REPO" --json title,body,labels`
2. The issue body already contains root cause analysis and affected files — use that as your guide
3. Read CLAUDE.md for project conventions before implementing
4. Implement the fix
5. Comment on the issue what was done, but do NOT close it — let the user verify

### `/issue view <number>` — View Issue Details
```bash
gh issue view <number> --repo "$REPO"
```

### `/issue search <query>` — Search Issues
```bash
gh issue list --repo "$REPO" --search "<query>" --json number,title,labels,state
```

### `/issue update <number> <changes>` — Update Issue
Update labels, title, or add a comment to an existing issue.

---

## Label System

Apply the most appropriate labels from these categories:

**Type:** `bug`, `enhancement`, `tech-debt`, `documentation`
**Priority:** `priority:high`, `priority:medium`, `priority:low`

Check the project's existing labels first (`gh label list --repo "$REPO"`) and use those. If the project has area-specific labels (e.g., `engine`, `api`, `ui`), apply them when relevant.

Rules:
- Every issue MUST have a type label and a priority label
- Bugs found during testing default to `priority:medium` unless clearly critical
- Create missing labels only if the project doesn't have an equivalent

---

## Rules
- All issue titles and descriptions MUST be in English
- Always investigate the code before creating an issue — never create shallow issues
- Include exact file paths with line numbers in the issue body
- Use `gh` CLI, not MCP GitHub tools
- Keep your response to the user brief — the detail goes into the GitHub issue
- If the user's description is too vague to investigate, ask a clarifying question before creating
- Read CLAUDE.md for project-specific conventions (branch targets, label systems, etc.)
