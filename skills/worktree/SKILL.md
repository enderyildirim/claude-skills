---
name: worktree
description: "Isolate parallel, independent tasks in separate git worktrees so they never overwrite each other — each task gets its own working directory and branch. Use when juggling multiple unrelated changes at once, or when the user says 'work on this in parallel', 'spin up a worktree', 'isolate this task'. Usage: /worktree [create <task> | list | remove <task> | clean]"
user-invocable: true
---

# Worktree — Isolate Parallel Tasks

Git worktrees let one repository have **multiple working directories checked out at once**, each on its own branch. This is the clean way to work on several independent tasks in parallel: changes in one never touch another, no stashing, no branch-switching churn, no half-finished edits bleeding across tasks.

Use this when you have multiple unrelated changes in flight (feature A, bugfix B, experiment C) and they must stay isolated.

## Conventions

- **Location:** worktrees live in a sibling directory `../<repo>-worktrees/<task-slug>` — outside the main checkout so they never nest inside it, never get caught by its `.gitignore`, and never confuse tooling.
- **Branch per task:** each worktree is on its own branch named `<task-slug>`, created off the repo's base branch (read it from CLAUDE.md; fall back to the default branch from `git symbolic-ref refs/remotes/origin/HEAD`).
- **Task slug:** short kebab-case derived from the task (e.g. "fix login redirect" → `fix-login-redirect`).
- **One branch, one worktree:** git refuses to check out the same branch in two worktrees — lean on that as the anti-overwrite guarantee.

## Process

First read the target project's `CLAUDE.md` for the base/PR branch convention. Then dispatch on the argument:

### `create <task>` (default action)
1. Resolve the repo root: `git rev-parse --show-toplevel` and its name.
2. Resolve the base branch (CLAUDE.md convention → else `git remote show origin` default).
3. Slugify `<task>` → `<task-slug>`.
4. Create the worktree on a fresh branch off the up-to-date base:
   ```bash
   git fetch origin <base> --quiet
   git worktree add ../<repo>-worktrees/<task-slug> -b <task-slug> origin/<base>
   ```
   If the branch already exists, drop `-b` and check it out instead.
5. Report the absolute path and tell the user how to enter it: `cd <path>` — and that they can open a **separate Claude Code session there** so each task has its own isolated context.

### `list`
Run `git worktree list` and present each worktree with its branch and a one-line status (clean / N uncommitted files via `git -C <path> status --short`). Flag any whose branch is already merged into base as **safe to remove**.

### `remove <task>`
1. Check the worktree is clean (`git -C <path> status --short`). If dirty, **stop and warn** — show the uncommitted changes, ask whether to commit, stash, or discard before removing. Never silently throw away work.
2. `git worktree remove ../<repo>-worktrees/<task-slug>` (add `--force` only after explicit user confirmation).
3. Offer to delete the branch if it's fully merged: `git branch -d <task-slug>`.

### `clean`
Run `git worktree prune` to clear stale administrative entries (worktrees whose directories were deleted manually), then `list` the survivors. Identify merged-branch worktrees and offer to remove them in a batch after confirmation.

## Parallel agents

For parallel work driven by Claude itself rather than separate human sessions, the Agent tool's `isolation: "worktree"` option runs each subagent in its own throwaway worktree — same isolation guarantee, auto-cleaned. Mention this when the user wants Claude to fan out edits across tasks concurrently rather than manage long-lived worktrees by hand.

## Rules

- **Never overwrite uncommitted work.** Before any `remove` or `--force`, surface dirty state and get a decision. This is the whole point of the skill.
- **Keep worktrees outside the main checkout** — always the sibling `../<repo>-worktrees/` dir, never a subdir of the repo.
- **One task = one branch = one worktree.** Don't reuse a branch across worktrees; don't pile unrelated changes into one worktree.
- **Branch off an up-to-date base** — fetch before creating so parallel tasks don't diverge from a stale point.
- **Be project-agnostic** — detect repo name, root, and base branch dynamically; never hardcode paths.
- **Show before destroying** — list what will be removed and confirm before deleting worktrees or branches.
