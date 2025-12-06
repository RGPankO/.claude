# Kiro Review Guide

> **Context Loading**: Reference from `/kiro-review` command. Load if not already in session.

## Purpose

Review completed tasks against Kiro specifications to verify correctness.

## Review Process

### Step 1: Load Specification Context

Read these files to understand requirements:

1. **Tasks file**: `.kiro/specs/{feature}/tasks.md`
   - Find exact task matching the task number
   - Extract description, sub-bullets, linked requirements
   - Note property tests marked with `*`

2. **Requirements file**: `.kiro/specs/{feature}/requirements.md`
   - Read requirements referenced by the task
   - Understand acceptance criteria

3. **Design file**: `.kiro/specs/{feature}/design.md`
   - Find relevant interfaces, types, database schema
   - Note correctness properties that apply

### Step 2: Identify Expected Implementation

Based on specs, determine:
- Files that should exist (source, test, config)
- Interfaces/Types to implement
- Database changes (Prisma schema, migrations)
- Test coverage (unit tests, property tests)
- Integration points

### Step 3: Examine Actual Implementation

Search codebase for what was implemented:
- `Glob` to find relevant files
- `Read` to examine implementation
- `Grep` to search for functions, interfaces, patterns
- Check for test files (`*.test.ts`, `*.property.test.ts`)

### Step 4: Generate Review Report

```markdown
## Task Review: [TASK_NUMBER]

### Task Description
> [Quote from tasks.md]

### Linked Requirements
| Requirement | Description | Status |
|-------------|-------------|--------|
| X.X | [Brief description] | ✅ Met / ⚠️ Partial / ❌ Missing |

### Implementation Checklist
- [x] Sub-item 1 - Status explanation
- [ ] Sub-item 2 - What's missing

### Files Reviewed
| File | Purpose | Exists | Correct |
|------|---------|--------|---------|
| path/to/file.ts | Description | ✅/❌ | ✅/⚠️/❌ |

### Correctness Analysis

**What's Done Well:**
- Point 1
- Point 2

**Issues Found:**
- Issue 1: Description and impact

**Missing Implementation:**
- What's not implemented yet

### Property Tests (if applicable)
| Property | Test File | Status |
|----------|-----------|--------|
| Property N: Name | path/to/test.ts | ✅/❌ |

### Recommendations
1. [Specific actionable recommendation]

### Overall Status
**Task [NUMBER]: [COMPLETE ✅ | PARTIAL ⚠️ | NOT STARTED ❌ | NEEDS FIXES 🔧]**

[Brief summary]
```

## Important Notes

- **DO NOT modify any files** - review only
- Focus on correctness according to spec, not style
- Check for logical errors, edge cases, spec compliance
- Property tests must use fast-check with `numRuns: 100`
- Cross-reference database schema with design.md
- Verify interfaces match design.md specifications
