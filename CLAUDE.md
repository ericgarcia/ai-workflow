# SuperClaude Entry Point

This file serves as the entry point for the SuperClaude framework.
You can add your own custom instructions and configurations here.

The SuperClaude framework components will be automatically imported below.

# ═══════════════════════════════════════════════════
# Project-Specific Rules
# ═══════════════════════════════════════════════════

## Test Report Auto-Management
**Priority**: 🟡 **Triggers**: Running tests that generate HTML reports

- **Auto-Report Detection**: After any test execution that generates HTML reports, automatically detect and display report locations
- **Auto-Open Reports**: Use the `./scripts/auto_open_reports.sh` script to open the most recent HTML report in browser
- **Report Location Printing**: Always print the full absolute path to any generated HTML reports
- **Test Completion Protocol**:
  1. Complete test execution
  2. Search for newly generated HTML reports (*.html in test outputs)
  3. Print full path(s) of any reports found
  4. Execute `./scripts/auto_open_reports.sh` to open the most recent report
  5. Confirm report opening with "✅ Report opened in browser!"

✅ **Right**: Run test → Print report location → Auto-open report → Confirm opening
❌ **Wrong**: Complete test without checking for or opening generated reports

**Detection Commands**:
```bash
# Find recent HTML reports
find . -name "*report.html" -type f -newermt "5 minutes ago"
# Auto-open most recent
./scripts/auto_open_reports.sh
```

# ═══════════════════════════════════════════════════
# SuperClaude Framework Components
# ═══════════════════════════════════════════════════

# Core Framework
@FLAGS.md
@PRINCIPLES.md
@RULES.md

# Behavioral Modes
@MODE_Brainstorming.md
@MODE_Introspection.md
@MODE_Orchestration.md
@MODE_Task_Management.md
@MODE_Token_Efficiency.md

# MCP Documentation
@MCP_Context7.md
@MCP_Magic.md
@MCP_Morphllm.md
@MCP_Playwright.md
@MCP_Sequential.md
@MCP_Serena.md
