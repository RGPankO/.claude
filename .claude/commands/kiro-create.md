---
argument-hint: <feature name and description>
description: Create a new Kiro spec (requirements.md, design.md, tasks.md)
---

# Kiro Spec Creation Command

## Instructions

If you haven't read these in this session, read them first:
- `.claude/docs/AGENT_GUIDE.md` - Base agent patterns
- `.claude/docs/KIRO_SPEC_GUIDE.md` - Spec writing guidelines

## Arguments

$ARGUMENTS - Feature name and brief description (e.g., "user-notifications: real-time alerts for order fills")

## Task

Create a complete Kiro spec following the guide:

1. **Clarify Scope** - Ask user about requirements if ambiguous
2. **Delegate Research** - Use `codebase-analyzer` to understand existing patterns, `investigator` for integration points
3. **Create Spec Folder** - `.kiro/specs/{feature-name}/`
4. **Write requirements.md** - User stories, EARS acceptance criteria, glossary
5. **Write design.md** - Architecture, interfaces, data models, correctness properties, testing strategy
6. **Write tasks.md** - Numbered checkboxes with requirement refs, property test tasks marked with `*`, checkpoints
7. **Report & Wait** - Present summary, wait for user review before finalizing

Key principles:
- Follow EARS format strictly (WHEN/GIVEN/SHALL)
- Include correctness properties with requirement refs
- Add visible increment checkpoints (UI-testable milestones)
- Delegate to agents for context gathering (anti-context-bloat)
