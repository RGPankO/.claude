# Spec Command - Comprehensive Guide

> **Context Loading**: This file is REFERENCED by the `/spec` command. Load it if not already in session.

## Purpose

Create comprehensive implementation plans for complex tasks that risk running out of context. The spec is saved to `.claude/specs/` so you can reference it throughout development without losing it when context resets.

## When to Use

- Task is large/complex enough that you might run out of context
- You want a persistent reference document during implementation
- Feature requires multiple phases or many files
- You need to track progress across context resets

## Workflow

### 1. Receive Task Description
User provides: `/spec how to implement X` or `/spec build feature Y`

### 2. Create the Spec
Analyze and create a comprehensive spec:
- Analyze requirements and complexity
- Break down into phases and tasks
- Identify dependencies and risks
- Create actionable task breakdown

### 3. Save Spec
Write the spec to `.claude/specs/<feature-name>-spec.md`
- Persists until manually deleted
- Can be referenced across context resets

### 4. Report Summary
Provide brief summary to user:
- Spec location
- Phase count and key milestones
- Immediate next steps
- How to reference the spec later

---

## Spec Document Structure

```markdown
# [Feature Name] Implementation Spec

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

## Referencing the Spec

After spec creation, user can:
- Read it: `Read .claude/specs/<feature>-spec.md`
- Ask Claude to check it: "What's the next task in the spec?"
- Update progress: Mark tasks complete as they're done
- Resume after context reset: Spec persists in .claude/specs/

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
- Make the spec too rigid
- Forget to check existing patterns first
- Plan more than 3-4 phases ahead in detail

---

## Example Usage

**User**: `/spec implement real-time order updates via WebSocket`

**Claude**:
1. Analyzes the task and creates comprehensive spec
2. Saves to `.claude/specs/realtime-order-updates-spec.md`
3. Reports back:

   ```
   Spec created: .claude/specs/realtime-order-updates-spec.md

   Phases: 3
   - Phase 1: WebSocket server setup
   - Phase 2: Order event broadcasting
   - Phase 3: Frontend integration

   Next step: Start with Phase 1, Task 1.1 - Create WebSocket service

   Reference the spec anytime with: "check the spec" or read it directly.
   ```

---

## Cleanup

Delete specs when:
- Feature is complete
- Spec is no longer needed
- Starting fresh on a different approach

```bash
rm .claude/specs/<feature>-spec.md
```
