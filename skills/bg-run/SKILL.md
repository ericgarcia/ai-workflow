---
name: bg-run
description: Run long-duration commands in background with immediate VS Code output viewing. Use for training, pipeline tasks, builds, or tests >30 seconds.
---

# Background Task Runner

Run long-duration tasks in background with automatic VS Code output monitoring.

## Instructions

When this skill is invoked, you MUST follow this exact two-step pattern:

### Step 1: Run the command in background

Use the Bash tool with `run_in_background: true` parameter:

```
Command: PYTHONUNBUFFERED=1 <the user's command> 2>&1
Parameter: run_in_background: true
```

**Critical flags:**
- `PYTHONUNBUFFERED=1` - Ensures Python output streams immediately
- `python -u` - Unbuffered Python output (add if running Python)
- `2>&1` - Capture both stdout and stderr

### Step 2: Immediately open output in VS Code

After Step 1 completes, you receive a task_id (e.g., "bb02ca5"). Immediately run:

```bash
code /tmp/claude/$CLAUDE_PROJECT_DIR_HASH/tasks/{task_id}.output
```

Where `$CLAUDE_PROJECT_DIR_HASH` is the hashed project path shown in the task output path.

## Example

User asks: "bg-run: train for 10 epochs"

**Step 1** - Bash with `run_in_background: true`:
```
PYTHONUNBUFFERED=1 uv run python -u -m models.segmenter.v0_unet.train --epochs 10 2>&1
```

**Step 2** - Open output (task_id was "abc1234"):
```bash
code /tmp/claude/-Users-ericg-code-fungal-segmentation-v10/tasks/abc1234.output
```

## When to Use

- Training runs (`python -m ... train`)
- Pipeline tasks (`python -m ... run_pipeline`)
- Test suites (`pytest`, `npm test`)
- Builds (`npm run build`, `cargo build`)
- Any command expected to take >30 seconds

## Key Requirements

1. **ALWAYS** use `run_in_background: true` parameter - never run synchronously
2. **ALWAYS** include `PYTHONUNBUFFERED=1` for Python commands
3. **ALWAYS** open output in VS Code immediately after starting
4. **NEVER** wait for task completion before opening VS Code - open immediately so user can monitor live

## Confirmation

After both steps, confirm to user:
- Task is running in background with task_id
- Output file is open in VS Code for live monitoring
