# Kiro Task Execution Command

Execute a specific task from the Kiro implementation plan following the standardized workflow.

## Usage

```
/kiro <task_number>
```

Examples:
- `/kiro 1.1` - Execute task 1.1
- `/kiro 4.3` - Execute task 4.3
- `/kiro 9.5` - Execute task 9.5

## Task Number Parameter

$ARGUMENTS

## Instructions

You are executing task **$ARGUMENTS** from the Kiro implementation plan.

### Step 0: Read the Guides

**If you have NOT already read these in this conversation**, read them first:

- `.claude/docs/AGENT_GUIDE.md` - Base agent patterns
- `.claude/docs/KIRO_TASK_EXECUTION_GUIDE.md` - Task execution workflow
- `.claude/docs/KIRO_TESTING_GUIDE.md` - Testing approach for this project

**If you have already read them**, proceed directly to executing the task.

### Execute the Task

Follow the Kiro Task Execution Guide workflow:

1. **Gather Context** - Read spec files and existing code
2. **Delegate** - Use agents to keep main context clean (see guide's Delegation section)
3. **Implement** - Write code following project patterns
4. **Test** - Meaningful, targeted tests (see guide's Testing Philosophy)
5. **Verify Integration** - Ensure new code is actually called (not orphaned)
6. **Report & Wait** - Present summary, wait for user confirmation

### Report Completion

When done, provide a summary and **wait for user confirmation**:

```
## Task $ARGUMENTS: READY FOR REVIEW

**Description:** [Task description]

**Requirements Satisfied:** X.Y, A.B, ...

**Files Created/Modified:**
- path/to/file.ts

**Integration Points:**
- ServiceA calls ServiceB.method() at [file:line]
- [or] Calculation result used in [file:line]

**Tests:** X passing

**Awaiting your confirmation to mark complete and stage files.**
```

## Important

- The guide at `.claude/docs/KIRO_TASK_EXECUTION_GUIDE.md` is the source of truth
- Always check if you've read it before re-reading (save context)
- Follow the guide's patterns exactly for consistency
- **DO NOT mark task complete** until user confirms
- **DO NOT stage files** until user confirms
- **DO NOT commit** - All commits require explicit user request
- **Verify integration** - A standalone service that nothing calls is NOT complete
