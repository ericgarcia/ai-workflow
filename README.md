# AI Workflow Configuration

Personal Claude Code configuration, skills, and workflow customizations.

## Installation

Clone directly to `~/.claude`:

```bash
# Backup existing config if present
mv ~/.claude ~/.claude.bak

# Clone repo
git clone git@github.com:ericgarcia/ai-workflow.git ~/.claude
```

Or if you already have Claude Code installed and want to merge:

```bash
cd ~/.claude
git init
git remote add origin git@github.com:ericgarcia/ai-workflow.git
git fetch origin
git reset --hard origin/main
```

## Structure

### Core Configuration

| File | Purpose |
|------|---------|
| `CLAUDE.md` | Main instructions and entry point |
| `FLAGS.md` | Behavioral flags (`--brainstorm`, `--think`, etc.) |
| `PRINCIPLES.md` | Software engineering principles |
| `RULES.md` | Behavioral rules and workflows |

### Modes

Behavioral modes that activate based on context:

| File | Purpose |
|------|---------|
| `MODE_Brainstorming.md` | Collaborative discovery for vague requirements |
| `MODE_Introspection.md` | Meta-cognitive analysis and self-reflection |
| `MODE_Orchestration.md` | Tool selection and parallel execution |
| `MODE_Task_Management.md` | Hierarchical task organization |
| `MODE_Token_Efficiency.md` | Compressed communication for large operations |

### MCP Server Documentation

Guides for when and how to use MCP servers:

| File | Purpose |
|------|---------|
| `MCP_Context7.md` | Library documentation lookup |
| `MCP_Magic.md` | UI component generation (21st.dev) |
| `MCP_Morphllm.md` | Pattern-based bulk code editing |
| `MCP_Playwright.md` | Browser automation and E2E testing |
| `MCP_Sequential.md` | Multi-step reasoning and analysis |
| `MCP_Serena.md` | Semantic code understanding and memory |

### Skills

Custom skills invoked with `/skill-name`:

| Skill | Command | Purpose |
|-------|---------|---------|
| `bg-run` | `/bg-run <cmd>` | Run long tasks in background with VS Code output |
| `wt` | `/wt sync\|land\|ship` | Git worktree workflow for parallel development |

### Agents

Specialized agent definitions for the Task tool:

- `backend-architect.md` - Backend system design
- `devops-architect.md` - Infrastructure and deployment
- `frontend-architect.md` - UI/UX implementation
- `learning-guide.md` - Teaching and explanation
- `performance-engineer.md` - Performance optimization
- `python-expert.md` - Python development
- `quality-engineer.md` - Testing strategies
- `refactoring-expert.md` - Code improvement
- `requirements-analyst.md` - Requirements discovery
- `root-cause-analyst.md` - Problem investigation
- `security-engineer.md` - Security analysis
- `socratic-mentor.md` - Guided learning
- `system-architect.md` - System design
- `technical-writer.md` - Documentation

### Commands

SuperClaude command definitions in `commands/sc/`:

- `analyze`, `brainstorm`, `build`, `cleanup`, `design`
- `document`, `estimate`, `explain`, `git`, `implement`
- `improve`, `index`, `load`, `reflect`, `save`
- `select-tool`, `spawn`, `task`, `test`, `troubleshoot`, `workflow`

### Plugins

Third-party plugins in `plugins/`. Currently includes marketplace plugins.

## Git Worktree Workflow

The `/wt` skill implements a solo development workflow using git worktrees:

```
/wt sync    # Update worktree with latest {base}
/wt land    # Land commits to {base}
/wt ship    # Ship {base} to main
/wt status  # Show worktree state
```

See `skills/wt/SKILL.md` for full documentation.

## What's Not Tracked

The following are gitignored (local state, caches, sensitive):

- `history.jsonl` - Conversation history
- `settings.local.json` - Local settings
- `cache/`, `debug/`, `logs/` - Temporary files
- `projects/` - Project-specific settings with local paths
- `plans/` - Auto-generated plan files
- `todos/` - Todo state
- `session-env/`, `shell-snapshots/` - Session state

## License

Personal configuration - use at your own discretion.
