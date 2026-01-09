# Git Worktree Workflow

Solo development workflow using multiple worktrees (e.g., parallel Claude Code sessions) with a linear integration branch.

## Naming Convention

**Integration branch** (`{base}`) - Your linear development branch:
- `develop` - Personal development branch
- `feature-auth` - Feature branch for a specific initiative
- `experiment-v3` - Experimental work

**Worktree folders** - Named after the project (stable):
- `{project}-wt-N` format (e.g., `{repo}-wt-1`, `{repo}-wt-2`)

**Worktree branches** - Named after the base branch they target:
- `{base}-wt-N` format (e.g., `feature-auth-wt-1`, `develop-wt-2`)

## Directory Structure

```
{repo}/                    # Main worktree - always on {base}
├── (project files)        # Used for land/ship operations, status checks
│
├── ../{repo}-wt-1/        # Worktree 1 - on branch {base}-wt-1
├── ../{repo}-wt-2/        # Worktree 2 - on branch {base}-wt-2
└── ../{repo}-wt-3/        # Worktree 3 - on branch {base}-wt-3
```

## Branch Structure

```
main (stable, releases)
  │
  └── {base} (integration branch, linear history) ← main worktree stays here
        │
        ├── {base}-wt-1 (in folder {repo}-wt-1/)
        ├── {base}-wt-2 (in folder {repo}-wt-2/)
        └── {base}-wt-3 (in folder {repo}-wt-3/)
```

**Rules:**
- `main` - Production-ready, only receives fast-forward merges from integration branches
- `{base}` - Your integration branch, always linear history. Main worktree stays here.
- `{base}-wt-N` - Worktree branches for parallel development (folder names are project-based)

---

## The Three Verbs

| Verb | What it does | When to use |
|------|--------------|-------------|
| **sync** | Update worktree with latest `{base}` | Before starting work, or to get changes from other worktrees |
| **land** | Move commits from worktree to `{base}` | When work is ready to integrate |
| **ship** | Land `{base}` to main (ff) | When ready to release or update production |

---

## sync - Update Worktree

Pull latest `{base}` into your worktree branch.

```bash
# In any worktree
git fetch origin
git rebase origin/{base}
git push --force-with-lease  # Update remote branch
```

**When to sync:**
- Starting a new day of work
- Before landing (required)
- After landing work from another worktree that you need in this one

**Visual:**
```
Before sync:
  {base}:       A → B → C → D
  {base}-wt-1:  A → B → X → Y

After sync:
  {base}:       A → B → C → D
  {base}-wt-1:  A → B → C → D → X' → Y'
```

---

## land - Integrate to Base

Move your worktree commits onto `{base}`, maintaining linear history.

```bash
# All from the worktree - no directory change needed
git fetch origin
git rebase origin/{base}
git push --force-with-lease              # update worktree branch on remote
git push origin HEAD:{base}              # fast-forward remote {base}
```

> **Note:** The main worktree can run `git pull --ff-only` later to sync locally.

**Visual:**
```
Before land:
  {base}:       A → B → C → D
  {base}-wt-1:  A → B → C → D → X' → Y'  (after sync)

After land:
  {base}:       A → B → C → D → X' → Y'
  {base}-wt-1:  A → B → C → D → X' → Y'  (same commits)
```

---

## ship - Land to Main

Fast-forward `main` to `{base}`, maintaining linear history. Two modes:

### Simple ship (no release tag)
For internal changes, refactoring, infrastructure updates.

```bash
git checkout main
git pull origin main
git merge --ff-only {base}
git push origin main
```

### Release ship (with tag)
For user-facing changes that warrant a version bump.

```bash
git checkout main
git pull origin main
git merge --ff-only {base}
git tag -a v1.2.3 -m "Release v1.2.3: <description>"
git push origin main --tags
```

**Visual:**
```
Before ship:
  main:   A ───────────────────────
  {base}: A → B → C → D → E → F → G

After ship:
  main:   A → B → C → D → E → F → G  (same commits, linear)
  {base}: A → B → C → D → E → F → G
```

---

## Quick Reference

### Using the `/wt` skill (Claude Code)
```
/wt sync    # Update worktree with latest {base}
/wt land    # Land commits to {base}
/wt ship    # Ship {base} to main
/wt status  # Show worktree state
```

### Daily workflow (manual)
```bash
# Morning: sync your worktree
/wt sync
# Or manually:
git fetch origin && git rebase origin/{base} && git push --force-with-lease

# Work, commit, work, commit...

# Ready to integrate: land
/wt land
# Or manually: see land section
```

### Starting a new worktree
```bash
# From main repo (pick next available number)
git worktree add ../{repo}-wt-3 -b {base}-wt-3 origin/{base}
cd ../{repo}-wt-3
git push -u origin {base}-wt-3
```

### Cleaning up after landing
```bash
# Optional: delete worktree branch after landing
git branch -d <worktree-branch>  # local
git push origin --delete <worktree-branch>  # remote

# Or keep it for continued work - just sync and continue
```

---

## Handling Conflicts

### During sync (rebase conflicts)
```bash
# Git will pause at each conflict
# Fix the conflict, then:
git add <fixed-files>
git rebase --continue

# If it's too messy, abort and try again:
git rebase --abort
```

### During land (ff-only fails)
This means `{base}` moved since you synced. Just sync again:
```bash
git checkout <your-worktree-branch>
git fetch origin
git rebase origin/{base}
git push --force-with-lease
# Then retry the land
```

---

## Mental Model

```
          sync                land                 ship
            │                   │                    │
            ▼                   ▼                    ▼
┌───────────────────┐   ┌─────────────┐       ┌──────────┐
│    {base}-wt-N    │ ─►│   {base}    │ ────► │   main   │
└───────────────────┘   └─────────────┘       └──────────┘
            │                   │
            └───────────────────┘
                  sync back
```

- **sync**: {base} → {base}-wt-N (rebase)
- **land**: {base}-wt-N → {base} (ff-merge)
- **ship**: {base} → main (ff-merge)
