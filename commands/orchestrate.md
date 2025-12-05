---
argument-hint: <task description>
description: Full workflow orchestration - analyze, implement, test, document, review
---

# Orchestrate Command

## Arguments

$ARGUMENTS - Task description (e.g., "add user notifications", "refactor auth module")

## Task

Execute a complete development workflow using specialized agents:

### Phase 1: Understand
- Use `investigator` or `codebase-analyzer` to understand current patterns
- Identify where changes should go and what patterns to follow

### Phase 2: Implement
- Use `senior-dev-implementer` for production-quality code
- Or `general-purpose` for simpler implementations

### Phase 3: Test
- Use `test-generator` to create comprehensive tests
- Ensure edge cases and error paths covered

### Phase 4: Document
- Use `docs-maintainer` to update relevant documentation
- Update ARCHITECTURE_GUIDE if patterns changed

### Phase 5: Review
- Use `senior-dev-consultant` to review the implementation
- Address any concerns raised

### Execution Rules

1. **Sequential phases** - Each phase depends on previous
2. **Report between phases** - Brief summary after each phase
3. **Stop on issues** - If any phase fails, stop and report
4. **No commits** - Wait for user to test and approve

### Agent Selection

| Phase | Primary Agent | Alternative |
|-------|---------------|-------------|
| Understand | `codebase-analyzer` | `investigator` (for bugs) |
| Implement | `senior-dev-implementer` | `general-purpose` (simple tasks) |
| Test | `test-generator` | - |
| Document | `docs-maintainer` | - |
| Review | `senior-dev-consultant` | `task-completion-validator` |

### Output

After each phase:
```
✓ Phase X Complete: [brief summary]
  - Files: [list]
  - Key changes: [list]
```

After all phases:
```
ORCHESTRATION COMPLETE

Files Modified: [list]
Tests Added: [count]
Docs Updated: [list]

Ready for testing. Please verify and confirm before commit.
```
