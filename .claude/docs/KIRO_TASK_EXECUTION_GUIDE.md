# Kiro Task Execution Guide

This document describes the exact workflow for executing tasks from a Kiro spec's `tasks.md` file. Follow this guide precisely to ensure consistent, high-quality task completion.

## Core Principles

1. **Context First** - Never start implementing without reading the spec documents
2. **One Task at a Time** - Complete and mark done before moving to next
3. **Requirements Traceability** - Every implementation must reference its requirements
4. **Test Everything** - No task is complete without passing tests
5. **Explicit Status Updates** - Always mark tasks in_progress → completed
6. **Integration Over Isolation** - New behavior must be wired into existing flows, not left as orphaned code
7. **Delegate Aggressively** - Use agents to keep main context clean (see Delegation section)

---

## Delegation (Anti-Context-Bloat)

**Delegate to agents** instead of loading many files into main context:

| Task Type | Agent | What to Give It |
|-----------|-------|-----------------|
| Understand patterns | `codebase-analyzer` | "Find how X is done in this codebase" |
| Trace integration | `investigator` | "Where is X called from?" |
| Complex implementation | `senior-dev-implementer` | Requirements + interface from design.md |
| Write tests | `test-generator` | Service file + requirements + testing constraints below |

**When delegating test writing**, include these constraints:

```
TESTING CONSTRAINTS:
- Write tests MANUALLY with intent - NO auto-generation
- Meaningful, targeted tests that validate requirements
- Max 3-5 tests per feature (happy path + 1-2 edge cases + error case)
- Test files under 150 lines
- No tests for trivial/obvious code
- No dozens of similar test cases
- Quality over quantity - thoughtful and precise
```

Only load into main context what you need to review/verify.

---

## Phase 1: Context Gathering

Before starting ANY task, read these files in this order:

### 1.1 Read requirements.md
Location: `.kiro/specs/{feature-name}/requirements.md`

Look for:
- User stories that relate to your task
- Acceptance criteria (in EARS format: WHEN/THEN/SHALL)
- Glossary terms you need to understand

### 1.2 Read design.md
Location: `.kiro/specs/{feature-name}/design.md`

Look for:
- Interfaces and method signatures relevant to your task
- Data models (Prisma schema) you'll work with
- Correctness Properties (for property test tasks)
- Error handling strategies

### 1.3 Read tasks.md
Location: `.kiro/specs/{feature-name}/tasks.md`

Understand:
- Your task's description and sub-bullets
- Requirements references (e.g., `_Requirements: 8.1, 9.1_`)
- What tasks came before (dependencies)
- Whether task is marked with `*` (optional/property test)

### 1.4 Read Existing Code
Before writing new code:
```
1. List the relevant service directory
2. Read existing service files to understand patterns
3. Read types.ts for existing type definitions
4. Read index.ts to see what's exported
5. Read existing tests to understand testing patterns
```

### 1.5 Trace the Integration Point (CRITICAL)

**If the task modifies behavior that happens "WHEN X occurs", you MUST find where X currently occurs in the codebase.**

Ask yourself:
- "WHEN does this trigger?" → Find that trigger in the code
- "WHO calls the method I'm modifying?" → Trace callers
- "WHERE does this flow currently happen?" → That's where you integrate

**How to trace:**
```bash
# Find where a behavior is triggered
grep -r "cancelOrder" src/          # Find all places orders are cancelled
grep -r "handleFill" src/           # Find all places fills are handled
grep -r "OrderStatus.CANCELLED" src/ # Find status change locations
```

**Common mistake to avoid:**
Creating a new method like `handlePartialFillCancellation()` but not calling it from anywhere. The method exists but the system never invokes it.

**Correct approach:**
1. Find where the trigger currently happens (e.g., `ActiveOrderService.cancelOrder`)
2. Modify THAT code to include the new behavior
3. Or call your new method FROM that existing code path

---

## Phase 2: Task Analysis

### 2.1 Parse the Task Structure

Tasks follow this format:
```markdown
- [ ] X.Y Task description
  - Implementation detail 1
  - Implementation detail 2
  - _Requirements: A.B, C.D, E.F_
```

Extract:
- **Task ID**: X.Y (e.g., 9.5)
- **Task Type**: Implementation, Property Test (`*`), or Checkpoint
- **Sub-bullets**: Specific things to implement
- **Requirements**: Cross-reference with requirements.md

### 2.2 Identify Task Type

| Marker | Type | What to Do |
|--------|------|------------|
| `- [ ] X.Y Implement...` | Implementation | Write service code + unit tests |
| `- [ ]* X.Y Write property test...` | Property Test | Write property-based tests only |
| `- [ ] X. Checkpoint...` | Checkpoint | Run all tests, verify passing |

### 2.3 Map Requirements to Implementation

For each requirement reference:
1. Find it in requirements.md
2. Read the acceptance criteria
3. Determine what code satisfies it
4. Plan how to test it

---

## Phase 3: Implementation

### 3.1 Mark Task In Progress
```
taskStatus(taskFilePath, taskId, "in_progress")
```
Use the EXACT task text from tasks.md.

### 3.2 For Implementation Tasks

**Create/Update Service File:**
```typescript
/**
 * Service Name
 * 
 * Brief description of what this service does.
 * 
 * Requirements:
 * - X.Y: Summary of requirement
 * - A.B: Summary of requirement
 */

export class ServiceName {
  /**
   * Method description
   * 
   * Requirement X.Y: What this method implements
   * 
   * @param input - Description
   * @returns Description
   */
  methodName(input: InputType): ReturnType {
    // Implementation with inline requirement comments
    // Req X.Y: Specific behavior being implemented
  }
}
```

**Update Exports:**
- Add to `types.ts` if new types needed
- Add to `index.ts` to export new functionality

### 3.3 For Behavioral Modifications (WHEN X, do Y)

When a requirement says "WHEN X happens, THE system SHALL do Y":

**Step 1: Find where X happens**
```
Requirement says: "WHEN a partially filled order is cancelled..."
Question: Where are orders cancelled?
Answer: grep -r "cancelOrder" src/ → ActiveOrderService.cancelOrder()
```

**Step 2: Modify the existing flow**
```typescript
// In the EXISTING cancelOrder method, add the new behavior:
async cancelOrder(orderId: string): Promise<void> {
  const order = await this.getOrder(orderId);

  // NEW: Check for partial fill before cancelling
  if (order.filledQuantity > 0) {
    await this.fillHandler.handlePartialFillCancellation(...);
  }

  // Existing cancellation logic...
}
```

**Step 3: Verify integration with an integration test**
```typescript
it('should handle partial fill when order is cancelled', async () => {
  // Arrange: Create order, simulate partial fill
  // Act: Call the ACTUAL cancel flow (not just the helper method)
  // Assert: Verify locked position created, unfilled released
});
```

**Anti-pattern to avoid:**
```typescript
// WRONG: Creating isolated method that nothing calls
class FillHandler {
  handlePartialFillCancellation(...) { ... }  // ← Orphaned code!
}

// Tests pass but feature doesn't work because nothing calls this method
```

### 3.5 For Property Test Tasks

**Create Property Test File:**
```typescript
/**
 * Property-Based Tests for {Feature}
 * 
 * **Feature: {feature-name}, Property {N}: {Property Name}**
 * **Validates: Requirements X.Y, A.B**
 */

import fc from 'fast-check';

describe('{Feature} Property Tests', () => {
  /**
   * **Feature: {feature-name}, Property {N}: {Property Name}**
   * **Validates: Requirements X.Y**
   * 
   * For any {input description}, the system SHALL {expected behavior}
   */
  it('should {property description}', () => {
    fc.assert(
      fc.property(
        // Generators for valid inputs
        fc.double({ min: X, max: Y, noNaN: true, noDefaultInfinity: true }),
        (input) => {
          // Property that must hold for ALL inputs
          return /* boolean expression */;
        }
      ),
      { numRuns: 100 }
    );
  });
});
```

### 3.6 For Checkpoint Tasks

Run full test suite:
```bash
npm test -- --runInBand
```

Report results to user. Only mark complete if ALL tests pass.

---

## Phase 4: Testing Strategy

### Testing Philosophy (CRITICAL)

**Tests must be meaningful, targeted, and manually written.**

| DO | DON'T |
|----|-------|
| Write tests with clear intent | Auto-generate test suites |
| 3-5 focused tests per feature | Dozens of similar test cases |
| Test behavior, not implementation | Test every line of code |
| Validate requirements | Write tests for the sake of coverage |
| Keep test files under 150 lines | Create 500+ line test files |

**Ask yourself**: "Does this test catch a real bug or validate a requirement?" If not, don't write it.

### 4.1 When to Write Unit Tests

Write unit tests for implementation tasks covering:

| Category | What to Test | Example |
|----------|--------------|---------|
| Happy Path | Normal successful operation | Create record with valid inputs |
| Calculations | Mathematical correctness | Profit = (sell - buy) * quantity |
| Edge Cases | Boundary values | Zero values, empty arrays |
| Error Handling | Invalid inputs, missing data | Non-existent ID, negative values |
| State Changes | Database updates | Status changes from A to B |

### 4.2 When to Write Property Tests

Write property tests when:
- Task is marked with `*`
- There's a corresponding Property in design.md Correctness Properties section
- Behavior should hold for ALL valid inputs (universal quantification)

### 4.3 Test Structure

**Unit Test Pattern:**
```typescript
describe('methodName', () => {
  it('should {behavior} (Req X.Y)', async () => {
    // Arrange: Set up test data
    const input = createTestInput();
    
    // Act: Execute the method
    const result = await service.method(input);
    
    // Assert: Verify the result
    expect(result.field).toBe(expectedValue);
  });
});
```

**Property Test Pattern:**
```typescript
it('should {property} for any valid input', () => {
  fc.assert(
    fc.property(
      generator1,
      generator2,
      (input1, input2) => {
        const result = functionUnderTest(input1, input2);
        return /* property holds */;
      }
    ),
    { numRuns: 100 }
  );
});
```

### 4.4 Test Coverage Guidelines

Per implementation task:
- 1-2 happy path tests
- 1-2 edge case tests  
- 1-2 error handling tests

Don't over-test - property tests handle input variety.

### 4.5 Running Tests

```bash
# Run specific test file
npm test -- --testPathPattern="{pattern}" --runInBand

# Run all tests
npm test -- --runInBand
```

Always use `--runInBand` for database tests to avoid parallel conflicts.

---

## Phase 5: Completion

### 5.1 Verify Tests Pass
Run tests and ensure ALL pass before marking complete.

### 5.2 Mark Task Complete
```
taskStatus(taskFilePath, taskId, "completed")
```

### 5.3 For Property Tests, Update PBT Status
```
updatePBTStatus(taskFilePath, taskId, "passed")
```

### 5.4 Check Parent Task
If all subtasks of a parent task are complete, mark the parent complete too.

---

## Common Patterns

### Database Test Setup
```typescript
let testUserId: string;
let testDeploymentId: string;

beforeAll(async () => {
  // Create test user and deployment
  const user = await prisma.user.create({ data: { username: `test_${Date.now()}`, ... } });
  testUserId = user.id;
  // ... create deployment
});

afterAll(async () => {
  // Clean up in reverse order of creation
  await prisma.childTable.deleteMany({ where: { deploymentId } });
  await prisma.deployment.delete({ where: { id: deploymentId } });
  await prisma.user.delete({ where: { id: testUserId } });
  await prisma.$disconnect();
});

beforeEach(async () => {
  // Clean up test-specific data between tests
  await prisma.targetTable.deleteMany({ where: { deploymentId } });
});
```

### Floating Point Comparisons
```typescript
// DON'T
expect(result).toBe(50250);

// DO
expect(result).toBeCloseTo(50250, 2);
```

### Requirement Comments
```typescript
// In service files
// Req 9.1: Mark Base Asset as Locked with fill price as Minimum Sell Price

// In test files
it('should mark base asset as locked (Req 9.1)', ...);
```

---

## Troubleshooting

### Test Failures
1. Read error message carefully
2. Check if it's a code bug or test bug
3. Fix and re-run
4. Only mark complete when passing

### Database Constraint Errors
- Use `--runInBand` flag
- Check cleanup order (children before parents)
- Use unique identifiers with timestamps

### Type Errors
- Run `getDiagnostics` to see all errors
- Fix imports
- Remove unused variables

---

## Quick Reference Checklist

Before marking ANY task complete:

**Context Gathering:**
- [ ] Read requirements.md for acceptance criteria
- [ ] Read design.md for interfaces and properties
- [ ] Read tasks.md for task details and requirements
- [ ] Read existing code for patterns
- [ ] Traced integration point (if behavioral change)

**Implementation:**
- [ ] Marked task as `in_progress`
- [ ] Implemented with requirement comments
- [ ] Wired new code into existing flows (not orphaned)
- [ ] Verified new methods are called from somewhere

**Testing:**
- [ ] Wrote unit tests for new methods
- [ ] Wrote integration test that exercises full flow (if behavioral change)
- [ ] Ran tests - ALL pass

**Completion:**
- [ ] Marked task as `completed`
- [ ] Updated PBT status (if property test)
- [ ] Checked if parent task should be marked complete

**Self-Review Questions:**
- "If I grep for calls to my new method, will I find any?"
- "Does my test call the actual entry point, or just the helper method?"
- "Is my new behavior actually reachable from the system's normal operation?"


---

## Deep Dive: The Mental Model

### Why Read in This Order?

**requirements.md first** because:
- Acceptance criteria define what "done" looks like
- You'll write tests against these criteria
- EARS format (WHEN/THEN/SHALL) maps directly to test assertions

**design.md second** because:
- Interfaces tell you method signatures to implement
- Data models tell you what fields exist in the database
- Correctness Properties tell you what property tests to write
- You implement TO the interface, not around it

**tasks.md third** because:
- Now you understand what the task is asking for
- Requirements references make sense
- You can see dependencies on previous tasks

### How to Decide What Methods to Create

1. **Read task sub-bullets** - Each bullet often = one method or one behavior
2. **Find matching interface in design.md** - The interface defines the method signature
3. **Check if method already exists** - Maybe you're extending, not creating
4. **One method per responsibility** - Don't create god methods

Example thought process:
```
Task says: "Calculate gross profit, fees, net profit"
Design.md shows: CompletedCycle has grossProfit, feesPaid, netProfit fields
Therefore: Need method that calculates these three values
```

### How to Approach Property Tests

1. **Find the Property in design.md** - Look for "Property N: Name"
2. **Read the universal statement** - "For any X, Y SHALL Z"
3. **Identify the input space** - What is X? What makes X valid?
4. **Create generators for valid X** - Use fast-check arbitraries
5. **Express Y SHALL Z as boolean** - The property returns true/false

Example thought process:
```
Property says: "For any buy price and profit margin, target >= buy price"
Input space: buy price (positive number), profit margin (0-100%)
Generator: fc.double({ min: 0.01, max: 100000 }), fc.double({ min: 0, max: 100 })
Property: calculateTarget(buy, margin) >= buy
```

### How to Handle Database Operations

1. **Check prisma/schema.prisma** - Know the model structure
2. **Check existing service patterns** - How do other services do CRUD?
3. **Use transactions for multi-step operations** - `prisma.$transaction()`
4. **Return the created/updated record** - Don't return void

### The Iteration Loop

```
implement → run tests → if fail → read error → fix → run tests → repeat
```

Never mark complete until tests pass. If stuck after 2-3 attempts, explain the issue and ask for guidance.

### Naming Conventions

Follow existing patterns:
- Services: `{domain}.service.ts` → `FillHandlerService`
- Tests: `{domain}.service.test.ts` or `{domain}.property.test.ts`
- Types: `{domain}/types.ts`
- Methods: camelCase, verb-first (`calculateProfit`, `handleFill`, `createOrder`)

---

## Tool Call Reference

### Mark Task In Progress
```typescript
taskStatus({
  taskFilePath: ".kiro/specs/{feature}/tasks.md",
  task: "X.Y Exact task text from file",
  status: "in_progress"
})
```

### Mark Task Complete
```typescript
taskStatus({
  taskFilePath: ".kiro/specs/{feature}/tasks.md", 
  task: "X.Y Exact task text from file",
  status: "completed"
})
```

### Update Property Test Status
```typescript
updatePBTStatus({
  taskFilePath: ".kiro/specs/{feature}/tasks.md",
  taskId: "X.Y Exact task text from file",
  status: "passed"  // or "failed" with failingExample
})
```

**Critical**: The task text must match EXACTLY what's in tasks.md, including the task number.

---

## Decision Trees

### "Should I write a unit test for this?"
```
Is it a calculation? → YES → Test with known inputs/outputs
Is it a database operation? → YES → Test CRUD works
Is it validation logic? → YES → Test valid and invalid inputs
Is it already covered by property test? → Maybe skip specific examples
```

### "Should I write a property test for this?"
```
Is task marked with *? → YES → Write property test
Is there a Property in design.md? → YES → Write property test
Does behavior hold for ALL inputs? → YES → Consider property test
Is it just a specific example? → NO → Unit test instead
```

### "Is this task done?"
```
Did I implement all sub-bullets? → Must be YES
Did I reference requirements in comments? → Must be YES
Did I write tests? → Must be YES
Do all tests pass? → Must be YES
Did I mark task complete? → Must be YES
```

### "Is this a behavioral modification task?"
```
Does requirement say "WHEN X, do Y"? → YES → Find where X happens
Does requirement say "on cancellation..."? → YES → Find cancellation code path
Does requirement say "during rebalance..."? → YES → Find rebalance code path
Does task modify existing system behavior? → YES → Must modify existing code
Am I creating new standalone code? → Ask "who calls this?"
```

### "Is my code actually integrated?"
```
grep for my new method name → Found callers? → Good
Run full flow test (not just unit test) → Behavior observable? → Good
Can I trigger this from normal system operation? → YES → Good
Is my method only called from my own tests? → BAD → Not integrated
```
