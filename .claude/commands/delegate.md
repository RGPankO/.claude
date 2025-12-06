---
argument-hint: <brief task description>
description: Delegate investigation and implementation to agents, keeping main context clean
---

# Delegate Command

## Instructions

If you haven't read these in this session, read them first:
- `.claude/docs/AGENT_GUIDE.md` - Base agent patterns
- `.claude/docs/DELEGATE_GUIDE.md` - Delegate-specific workflows

## Arguments

$ARGUMENTS - Brief task description (e.g., "fix login flow", "investigate CSS errors")

## Task

Act as a manager delegating to specialized agents:

1. **Parse Task** - Identify investigation vs implementation needs
2. **Choose Strategy**:
   - **Large tasks**: Mini-delegate sequentially (investigate → backend → frontend → tests)
   - **Simple tasks**: Full delegate in parallel
3. **Select Agents** - investigator, docs-explorer, codebase-analyzer, senior-dev-consultant, general-purpose
4. **Launch Agents**:
   - **Parallel**: Independent tasks in single message
   - **Sequential**: Dependent tasks one-by-one, using findings to inform next
5. **Receive Reports** - Demand file paths, line numbers, code snippets
6. **Decide & Implement** - Auto-fix small bugs; ask approval for large changes
7. **Report & Wait** - Summarize changes, wait for user to test
8. **Commit Only After Testing** - Never commit until user confirms working

Key principles:
- Never read files yourself. Delegate everything. Keep context clean.
- For large tasks, mini-delegate portions for better control (see guide for details).
