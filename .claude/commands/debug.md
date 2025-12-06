---
argument-hint: [description of bug/issue]
description: First-principles debugging for complex issues
---

# Debug Command

## Instructions

If you haven't read these in this session, read them first:
- `.claude/docs/AGENT_GUIDE.md` - Base agent patterns (for investigator agents)
- `.claude/docs/DEBUG_GUIDE.md` - Systematic debugging process

## Arguments

$ARGUMENTS - Description of the bug/issue to debug.

## Task

Follow the systematic debugging process:

1. **STOP AND ASSESS** - Categorize: KNOWN (verified), ASSUMED (unverified), UNKNOWN
2. **MAP EXECUTION FLOW** - Trace trigger → function chain → error location
3. **LAUNCH INVESTIGATORS** - Use investigator agents for each unknown
4. **ADD INSTRUMENTATION** - Entry/exit logging, state checks, dependency verification
5. **COLLECT EVIDENCE** - Run with logging, get user to verify browser/network state
6. **FORM HYPOTHESIS** - Based on evidence, test predictions
7. **FIX ROOT CAUSE** - Only after hypothesis confirmed

Key principle: Evidence over intuition. Question assumptions. Make the invisible visible.
