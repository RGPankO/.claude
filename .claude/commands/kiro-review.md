---
argument-hint: <task_number>
description: Review completed Kiro tasks against specifications
---

# Kiro Review Command

## Instructions

If you haven't read `.claude/docs/KIRO_REVIEW_GUIDE.md` in this session, read it first.

## Arguments

$ARGUMENTS - Task number to review (e.g., "1.1", "4.3", "9.5")

## Task

Review task **$ARGUMENTS** following the guide:

1. **Load Specs** - Read tasks.md, requirements.md, design.md for the feature
2. **Identify Expected** - What files, interfaces, tests should exist?
3. **Examine Actual** - Search codebase for implementation
4. **Generate Report** - Use the report template from the guide

Key principles:
- DO NOT modify files - review only
- Focus on spec compliance, not style
- Verify property tests use `numRuns: 100`
- Check interfaces match design.md
