# Plan Command - Comprehensive Guide

> **Context Loading**: This file is REFERENCED by the `/plan` command. Load it if not already in session.

## Purpose

Create comprehensive implementation plans for complex tasks that risk running out of context. The plan is saved to `tools/tmp/` (gitignored) so you can reference it throughout development without losing it when context resets.

## When to Use

- Task is large/complex enough that you might run out of context
- You want a persistent reference document during implementation
- Feature requires multiple phases or many files
- You need to track progress across context resets

## Workflow

### 1. Receive Task Description
User provides: `/plan how to implement X` or `/plan build feature Y`

### 2. Create the Plan
Analyze and create a comprehensive plan:
- Analyze requirements and complexity
- Break down into phases and tasks
- Identify dependencies and risks
- Create actionable task breakdown

### 3. Save Plan to tmp/
Write the plan to `tools/tmp/<feature-name>-plan.md`
- File is gitignored (not version controlled)
- Persists until manually deleted
- Can be referenced across context resets

### 4. Report Summary
Provide brief summary to user:
- Plan location
- Phase count and key milestones
- Immediate next steps
- How to reference the plan later

---

## Plan Document Structure

```markdown
# [Feature Name] Implementation Plan

**Created**: [Date]
**Status**: Planning | In Progress | Completed

## Overview
[2-3 sentence summary of what we're building]

## Requirements
- [Key requirement 1]
- [Key requirement 2]

## Technical Approach
[High-level architecture/approach description]

## Implementation Phases

### Phase 1: [Foundation]
**Goal**: [What this phase accomplishes]

Tasks:
- [ ] Task 1.1: [Description]
- [ ] Task 1.2: [Description]

### Phase 2: [Core Implementation]
Tasks:
- [ ] Task 2.1: [Description]
- [ ] Task 2.2: [Description]

### Phase 3: [Integration & Testing]
Tasks:
- [ ] Task 3.1: [Description]
- [ ] Task 3.2: [Description]

## Risks & Considerations
- [Risk 1]: [Mitigation]
- [Risk 2]: [Mitigation]

## Progress Tracking
- [ ] Phase 1 complete
- [ ] Phase 2 complete
- [ ] Phase 3 complete

## Notes
[Space for discoveries, decisions, adjustments during implementation]
```

---

## Referencing the Plan

After plan creation, user can:
- Read it: `Read tools/tmp/<feature>-plan.md`
- Ask Claude to check it: "What's the next task in the plan?"
- Update progress: Mark tasks complete as they're done
- Resume after context reset: Plan persists in tmp/

---

## Best Practices

### DO:
- Keep phases small (3-5 tasks each)
- Make tasks specific and actionable
- Include validation checkpoints
- Note integration points with existing code
- Leave space for notes/discoveries

### DON'T:
- Over-plan implementation details
- Include time estimates
- Make the plan too rigid
- Forget to check existing patterns first
- Plan more than 3-4 phases ahead in detail

---

## Example Usage

**User**: `/plan implement real-time order updates via WebSocket`

**Claude**:
1. Analyzes the task and creates comprehensive plan
2. Saves to `tools/tmp/realtime-order-updates-plan.md`
3. Reports back:

   ```
   Plan created: tools/tmp/realtime-order-updates-plan.md

   Phases: 3
   - Phase 1: WebSocket server setup
   - Phase 2: Order event broadcasting
   - Phase 3: Frontend integration

   Next step: Start with Phase 1, Task 1.1 - Create WebSocket service

   Reference the plan anytime with: "check the plan" or read it directly.
   ```

---

## Cleanup

Plans in `tools/tmp/` are gitignored and temporary. Delete when:
- Feature is complete
- Plan is no longer needed
- Starting fresh on a different approach

```bash
rm tools/tmp/<feature>-plan.md
```
