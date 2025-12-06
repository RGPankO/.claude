---
argument-hint: <task description>
description: Full workflow orchestration - analyze, implement, test, document, review
---

# Orchestrate Command

## Instructions

If you haven't read these in this session, read them first:
- `.claude/docs/AGENT_GUIDE.md` - Base agent patterns
- `.claude/docs/ORCHESTRATE_GUIDE.md` - Orchestration constraints and phases

## Arguments

$ARGUMENTS - Task description (e.g., "add user notifications", "refactor auth module")

## Task

Execute focused workflow with constraints from the guide:

1. **Understand** - Use `codebase-analyzer` to understand patterns
2. **Implement** - Use `senior-dev-implementer` with guide constraints
3. **Test** - Use `test-generator` (skip if not needed, see guide)
4. **Document** - Use `docs-maintainer` (skip unless explicitly asked)
5. **Review** - Use `senior-dev-consultant` to check for red flags

Key principles:
- Include CONSTRAINTS block from guide in every agent call
- Verify file line counts (max 300)
- Stop on red flags, fix before proceeding
- No commits until user approves
