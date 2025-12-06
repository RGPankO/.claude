---
argument-hint: [description of what to plan] (required: description of the feature or system to plan)
description: Create comprehensive strategic plan for complex features, saved to tmp for persistence
---

# Spec Command

## Instructions

If you haven't read `.claude/docs/SPEC_GUIDE.md` in this session, read it first.

## Arguments

$ARGUMENTS - Description of what to plan (e.g., "implement WebSocket notifications", "build user dashboard")

## Task

1. **Analyze & Plan** - Create a comprehensive implementation plan:
   - Break down into 2-4 phases with clear goals
   - Create specific, actionable tasks (developer-sized chunks)
   - Identify dependencies between tasks
   - Note risks and mitigation strategies
   - No time estimates, just work breakdown

2. **Save Spec** - Write the spec to `.claude/specs/<feature-name>-spec.md`
   - Extract a short feature name from the description
   - File persists until manually deleted

3. **Report Back** - Provide brief summary:
   - Spec file location
   - Number of phases and key milestones
   - Immediate next step to begin

Key purpose: Create a persistent reference document for complex tasks that might outlast the context window.
