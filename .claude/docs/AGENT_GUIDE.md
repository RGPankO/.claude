# Agent Guide - Base Patterns

> **Context Loading**: Reference this guide from any command that uses agents. Load if not already in session.

## Why Use Agents

Agents keep your main context clean. Instead of loading files, reading code, and bloating context:
- **Delegate** the work to specialized agents
- **Receive** concise reports with only essential info
- **Decide** based on findings, not raw data

## Available Agents

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| `investigator` | Bug hunting, root cause analysis | "Why is X broken?", "Find the cause" |
| `codebase-analyzer` | Structure, patterns, architecture | "Where should this go?", "Find patterns" |
| `docs-explorer` | Documentation research | "How does X library work?" |
| `senior-dev-consultant` | Expert advice, architecture | "Review approach", "Security concerns?" |
| `senior-dev-implementer` | Production-quality code | Complex features needing senior quality |
| `test-generator` | Create focused test suites | "Generate tests for X" |
| `task-completion-validator` | Verify completeness | Before marking tasks done |
| `docs-maintainer` | Update documentation | After significant changes |
| `general-purpose` | Implementation | "Create X", "Fix Y", "Update Z" |

## Agent Selection Patterns

**Bug Fix:**
1. `investigator` → find root cause
2. `general-purpose` → implement fix

**New Feature:**
1. `codebase-analyzer` → understand existing patterns
2. `senior-dev-implementer` → implement feature
3. `test-generator` → add tests

**Research/Understanding:**
1. `docs-explorer` → external docs/APIs
2. `codebase-analyzer` → internal code patterns

## Parallel vs Sequential

```
Independent tasks → PARALLEL (single message, multiple Task calls)
  Example: "Investigate auth" + "Investigate database" - no dependency

Dependent tasks → SEQUENTIAL (wait for report, then next)
  Example: "Investigate patterns" → "Implement based on findings"
```

**Rule**: If the next delegation would change based on what an agent finds, run sequentially.

## Report Standards

**Every agent report MUST include:**

1. **Precise locations**: Full file paths + line numbers
2. **Code snippets**: Exact code (5-10 lines), BEFORE/AFTER for changes
3. **Root cause**: WHY, not just what
4. **Actionable fix**: Exact changes needed

**Report Template:**
```markdown
## Findings

**Root Cause:** [One sentence]

**Evidence:**
- File: `src/path/to/file.ts:42`
- Code: [exact snippet]
- Issue: [what's wrong]

**Fix Required:**
- File: `src/path/to/file.ts:42`
- Change: [exact change]
- Reason: [why]
```

## Agent Instructions Template

When launching an agent, include:

```
TASK: [Specific task description]

SCOPE: [What to look at, what to ignore]

RETURN FORMAT:
- File paths with line numbers
- Code snippets (5-10 lines)
- Root cause analysis
- Specific actionable recommendations

DO NOT:
- [Things to avoid]
- Load unnecessary files into report
```

## Anti-Patterns

| Don't | Do Instead |
|-------|------------|
| Read files yourself | Delegate to agent |
| Keep full reports in context | Store only summaries |
| Vague instructions | Specific, scoped tasks |
| Run dependent tasks in parallel | Sequential with findings |
| Commit before user tests | Always wait for user confirmation |
