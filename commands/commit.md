---
argument-hint: [instructions] (optional: "exclude X", "only Y", "with message 'Z'", specific files)
description: Smart commit with logical grouping
---

# Commit Command

## Instructions

If you haven't read `.claude/docs/COMMIT_GUIDELINES.md` in this session, read it first.

## Arguments

$ARGUMENTS - Optional instructions for filtering/customizing the commit.

## Task

1. Run git status to see all changes
2. Apply any user instructions from $ARGUMENTS
3. Group changes logically per the guidelines
4. Create commits with clear messages
5. Report what was committed and what was skipped
