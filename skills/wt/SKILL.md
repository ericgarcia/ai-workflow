---
name: wt
description: Git worktree workflow with three verbs - sync, land, ship. For solo development with multiple parallel worktrees and linear history.
---

# Git Worktree Workflow

Manage git worktrees with three simple verbs: **sync**, **land**, **ship**.

## Commands

- `/wt sync` - Update current worktree with latest `{base}`
- `/wt land` - Land commits from current worktree to `{base}`
- `/wt ship` - Ship `{base}` to main (fast-forward)
- `/wt status` - Show worktree state and relationship to `{base}`

## Branch Detection

Detect `{base}` from current branch name by stripping `-wt-N` suffix:
- `feature-auth-wt-1` → `{base}` = `feature-auth`
- `develop-ericg-wt-2` → `{base}` = `develop-ericg`

Use this command to detect:
```bash
git branch --show-current | sed 's/-wt-[0-9]*$//'
```

## /wt sync

Update worktree branch with latest `{base}`. Run this before starting work or to get changes landed from other worktrees.

**Prerequisites:**
- Must be in a worktree (branch name ends with `-wt-N`)
- Working directory should be clean

**Steps:**
```bash
git fetch origin
git rebase origin/{base}
git push --force-with-lease
```

**Conflict handling:**
If rebase conflicts occur:
1. Show the user which files have conflicts
2. For each conflicting file, show the conflict markers and ask how to resolve
3. Apply the resolution with `git add <file>`
4. Continue with `git rebase --continue`
5. Repeat until rebase completes
6. Push with `--force-with-lease`

NEVER use `git rebase -i` (interactive). Handle conflicts by showing them to the user and asking for resolution guidance.

## /wt land

Land commits from worktree to `{base}`. This integrates your work into the base branch.

**Prerequisites:**
- Must be in a worktree (branch name ends with `-wt-N`)
- Working directory should be clean

**Steps:**
```bash
# Sync first (rebase onto latest {base})
git fetch origin
git rebase origin/{base}
git push --force-with-lease

# Fast-forward {base} on remote
git push origin HEAD:{base}
```

**After landing:**
- Inform user that `{base}` has been updated on remote
- Suggest: "Run `git pull --ff-only` in your main worktree to sync locally"

**Conflict handling:** Same as sync - show conflicts, ask user for resolution, apply, continue.

## /wt ship

Ship `{base}` to main. This releases integrated work to the main branch.

**Prerequisites:**
- Should be in main worktree (branch is `{base}`, not `{base}-wt-N`)
- Or can be run from anywhere if user confirms

**Steps:**
```bash
git fetch origin
git checkout main
git pull --ff-only origin main
git merge --ff-only origin/{base}
git push origin main
```

**If ff-only fails:** The user needs to land more recent work first, or main has diverged. Ask user how to proceed.

**Optional release tag:**
Ask user: "Do you want to tag this as a release?"
If yes, ask for version number and create tag:
```bash
git tag -a v{version} -m "Release v{version}: {description}"
git push origin main --tags
```

## /wt status

Show current worktree state.

**Information to display:**
1. Current branch name
2. Detected `{base}` (or note if in main worktree)
3. Commits ahead/behind `origin/{base}`
4. Working directory status (clean/dirty)
5. List of all worktrees: `git worktree list`

## Safety Checks

Before any destructive operation:
1. Check if working directory is dirty (`git status --porcelain`)
2. If dirty, warn user and ask if they want to stash or abort
3. Confirm before force-push operations

## Error Messages

- **Not in a worktree:** "This command must be run from a worktree branch (ending in -wt-N)"
- **Dirty working directory:** "Working directory has uncommitted changes. Stash or commit first?"
- **FF-only failed:** "Fast-forward failed. The target branch has diverged. You may need to sync first."
