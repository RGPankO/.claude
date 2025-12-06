# Orchestrate Guide

Constraints and rules for `/orchestrate` command to ensure focused, controlled execution.

## Core Philosophy

Orchestration should be **surgical, not expansive**. Do exactly what's asked, nothing more.

## Agent Constraints

When calling ANY agent during orchestration, include these constraints in the prompt:

```
CONSTRAINTS:
- Do ONLY what is specified below, nothing more
- Max 300 lines per file (split if larger)
- Max 50 lines per function (extract helpers if longer)
- Follow existing project patterns exactly
- No "improvements" or "enhancements" beyond scope
- No new documentation unless explicitly asked
- Ask if uncertain about scope

TASK: [specific task here]
```

## Phase-Specific Rules

### Implementer (senior-dev-implementer)

**MUST:**
- Split files exceeding 300 lines into multiple focused files
- Extract functions exceeding 50 lines into helpers
- Follow existing naming and structure patterns
- Report file line counts in output

**MUST NOT:**
- Create monolithic 500+ line files
- Add features not explicitly requested
- Refactor unrelated code
- Add documentation files
- Over-engineer the solution

**File splitting example:**
```
# Instead of one 600-line file:
src/components/BigFeature.tsx (600 lines) ❌

# Split into focused files:
src/components/feature/
  index.tsx (50 lines)      - Main export, composition
  FeatureForm.tsx (100 lines)
  FeatureList.tsx (80 lines)
  FeatureItem.tsx (70 lines)
  hooks.ts (100 lines)       - Custom hooks
  types.ts (30 lines)        - Type definitions
```

### Test Generator (test-generator)

**MUST:**
- Write tests manually with intent (NO auto-generation)
- Keep test files under 150 lines
- Write minimal tests: happy path + 1-2 edge cases
- Follow existing test patterns in project

**MUST NOT:**
- Auto-generate comprehensive test suites
- Create 500+ line test files
- Write dozens of similar test cases
- Test obvious/trivial code
- Duplicate existing test coverage

**Typical test scope:**
```javascript
describe('featureName', () => {
  it('should handle normal case', () => { ... });
  it('should handle main edge case', () => { ... });
  it('should throw on invalid input', () => { ... });
});
// That's it. Stop. 3-5 tests max.
```

### Docs Maintainer (docs-maintainer)

**MUST:**
- Only update what's explicitly requested
- Verify which tasks were actually completed before marking
- Keep changes surgical and minimal

**MUST NOT:**
- Create new documentation files
- Add new sections to existing docs
- Document patterns you observed
- Add "lessons learned" or tips
- Mark tasks complete that weren't worked on
- Expand documentation scope

### Reviewer (senior-dev-consultant)

**MUST CHECK:**
- File sizes (flag anything over 300 lines)
- Scope creep (flag additions not requested)
- Unwanted files (flag unexpected docs, tests)
- Pattern compliance (flag deviations from project style)

## When to Skip Phases

### Skip Test Phase if:
- Simple change (config update, typo fix)
- Existing tests already cover the change
- User didn't ask for tests
- Change is to non-logic code (CSS, copy)

### Skip Doc Phase if:
- User didn't explicitly ask for doc updates
- No task status needs updating
- Change doesn't affect documented patterns

## Output Standards

**After each phase:**
```
✓ Phase X: [1-line summary]
  Files: [list with line counts]
```

**Final report:**
```
COMPLETE

Files: [list with line counts]
Tests: [count] (if any)

Ready for review.
```

## Red Flags to Watch For

If you see any of these, STOP and fix before proceeding:

- ❌ File exceeds 300 lines
- ❌ Test file exceeds 150 lines
- ❌ Agent created files not requested
- ❌ Agent added "nice to have" features
- ❌ Agent created documentation files
- ❌ Agent marked unrelated tasks complete
- ❌ Agent added new sections to docs
