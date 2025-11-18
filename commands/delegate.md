---
argument-hint: <brief task description>
description: Delegate investigation and implementation to agents, keeping main context clean
---

# Delegate Command

## Purpose
Delegate complex tasks to specialized agents for investigation and implementation while keeping the main Claude instance's context minimal and focused on management and decision-making.

## Command Syntax
- `/delegate <task>` - Brief task description (e.g., "fix login flow", "investigate CSS errors")

## Command Instructions

**Arguments received:** `$ARGUMENTS`

### Core Principles

When user invokes `/delegate`, you MUST:

1. ✅ **Delegate ALL investigation** to specialized agents
2. ✅ **Receive reports back** - agents return concise findings only
3. ✅ **Act as manager** - review reports, make decisions, delegate implementation
4. ✅ **Keep YOUR context minimal** - NEVER read files or code yourself
5. ✅ **Run agents in parallel** when tasks are independent
6. ❌ **NEVER commit changes** until user explicitly tests first
7. ✅ **Brief descriptions OK** - agents will fill in implementation details

### Workflow Pattern

**Step 1: Parse Task**
- Extract the core task from `$ARGUMENTS`
- Identify what needs investigation vs implementation
- Determine which specialized agents to use

**Step 2: Create Agent Tasks**
Create 1-3 specialized agent tasks:
- `investigator` - Debug issues, find root causes, analyze code
- `docs-explorer` - Research documentation without loading into context
- `codebase-analyzer` - Understand project structure and patterns
- `senior-dev-consultant` - Expert second opinions on complex decisions
- `general-purpose` - Implement fixes, create files, modify code

**Step 3: Run Agents**
- Launch agents with clear, specific instructions
- Request concise reports with PRECISE details (see Report Standards below)
- Run independent agents in parallel (single message, multiple Task calls)
- Demand file paths, line numbers, code snippets in all reports

**Step 4: Receive & Summarize**
- Get reports from all agents (verify they follow Report Standards)
- **Store detailed reports** in your context (file paths, line numbers, what/where/why)
- Synthesize findings into brief summary for user
- Identify specific fixes needed
- **Keep enough detail** to answer user questions without re-investigating

**Step 5: Decide Whether to Ask for Approval**
- **Auto-implement without asking when:**
  - Small, obvious bug fix (1-5 line change)
  - Clear, single solution with no alternatives
  - No risk of side effects or breaking changes
  - Investigation report is confident and clear
- **Ask for approval when:**
  - Large changes (multiple files, architectural impact)
  - Multiple possible solutions or approaches
  - Potential side effects or breaking changes
  - Uncertainty about the best approach
  - User explicitly requests review first

**Step 6: Implement**
- Delegate implementation to agents if complex
- Make changes based on agent reports
- Test locally if possible

**Step 7: Report Completion**
- Summarize what was changed
- List files modified
- **DO NOT commit** - inform user testing is required first

**Step 8: User Tests**
- Wait for user to test in browser/terminal
- User reports "it works" or describes issues
- Only after user confirms → offer to commit

### Agent Selection Guide

**investigator**: Debugging, root cause analysis, finding issues
- "Why is X not working?"
- "Find the bug causing Y"
- "Trace the flow of Z"

**docs-explorer**: Documentation research, API reference lookup
- "How does X library work?"
- "What are the config options?"
- "Find translation structure"

**codebase-analyzer**: Architecture understanding, pattern discovery
- "Where should new feature go?"
- "What's the current structure?"
- "Find existing patterns"

**senior-dev-consultant**: Expert technical guidance, second opinions
- "Review this architectural decision"
- "Best approach for implementing X?"
- "Security review of authentication flow"
- "Performance optimization strategy"
- "Is this the right design pattern?"

**general-purpose**: File creation, modification, implementation
- "Create component X"
- "Fix the bug in Y"
- "Update configuration Z"

### Example Usage

**User:** `/delegate fix login persistence`

**Claude Response:**
1. Creates investigator task: "Why doesn't login persist after magic link?"
2. Gets report: "AuthContext only checks localStorage on mount, race condition"
3. Summarizes to user: "Found race condition - redirect happens before token stored"
4. Creates general-purpose task: "Remove 2-second delay from verify page"
5. Reports completion: "Fixed - redirect now immediate, removed setTimeout"
6. Waits: "Ready to test - please verify login persists after clicking magic link"

**User:** `/delegate port Bulgarian education content`

**Claude Response:**
1. Creates docs-explorer task: "Extract Bulgarian content from btc_reference"
2. Creates codebase-analyzer task: "Find current education content structure"
3. Gets reports with structure comparisons
4. Summarizes: "Reference has 3 complete files, need to transform HTML → JSX"
5. Creates general-purpose task: "Transform 3 Bulgarian files"
6. Reports: "Created bg/fiat-experiment.js, bg/why-bitcoin.js, bg/why-not-gold.js"
7. Waits: "Test at /bg/education/fiat-experiment - should show Bulgarian content"

**User:** `/delegate investigate API 404 errors`

**Claude Response:**
1. Creates investigator task: "Find missing API routes causing 404s"
2. Gets report: "/api/assets/prices and /api/portfolio don't exist - Phase 7 not implemented"
3. Summarizes: "These are expected - Phase 7 (Portfolios) not built yet"
4. No fix needed: "These 404s are normal for current phase"

### Report Standards (CRITICAL)

**Every Agent Report MUST Include:**

1. **Precise File Locations**
   - FULL file paths: `C:\projects\measured_in_btc\btc_new\src\components\Example.jsx`
   - Exact line numbers: `Line 42` or `Lines 38-45`
   - Multiple locations if relevant

2. **Code Context**
   - Exact code snippets (5-10 lines showing the issue/change)
   - BEFORE/AFTER comparisons for changes
   - Surrounding context when needed

3. **Root Cause Analysis**
   - Why the issue exists (not just what)
   - What caused the problem
   - What needs to change and why

4. **Implementation Details**
   - Specific functions/variables affected
   - Dependencies and relationships
   - Side effects or considerations

5. **Actionable Fixes**
   - Exact changes needed (not vague suggestions)
   - Line-by-line modifications when implementing
   - Files created/deleted/modified list

**WHY This Matters:**

✅ **For next agent:** Can implement immediately without re-investigation
✅ **For main Claude:** Understands what/where/why without reading files
✅ **For user questions:** Can answer specifics without new agent investigation
✅ **For efficiency:** No redundant work, clean handoffs between agents

**Report Format Example:**
```markdown
## Investigation Results

**Root Cause:**
[One sentence explanation]

**Evidence:**
- File: `C:\path\to\file.js:42`
- Code: [exact snippet]
- Issue: [what's wrong]

**Fix Required:**
- File: `C:\path\to\file.js:42`
- Change: [exact change]
- Reason: [why]

**Side Effects:**
[Any related changes needed]
```

### Quality Standards

**Agent Instructions Must:**
- Be specific and actionable
- Define exact scope
- Request specific return format (enforce Report Standards)
- Include context about the project
- Specify what NOT to do
- Demand file paths and line numbers in response

**Agent Reports Should:**
- Follow Report Standards above (file paths, line numbers, code snippets)
- Be concise but complete (all essential info, no fluff)
- Enable implementation without re-investigation
- Allow main Claude to answer user questions without new agents
- State root cause and "why" clearly

**Your Context Should:**
- Only hold agent summaries (not full reports)
- Track what's been investigated
- Store user's latest feedback
- Keep implementation plan minimal

### Anti-Patterns to Avoid

❌ Reading files yourself instead of using agents
❌ Loading large code blocks into your context
❌ Implementing without investigation first
❌ Committing before user tests
❌ Vague agent instructions
❌ Not running parallel agents when possible
❌ Keeping full agent reports in context

### Success Criteria

✅ Your context stays under 50% usage
✅ Agent reports are concise and actionable
✅ Fixes are based on investigation findings
✅ User tests before any commits
✅ Tasks complete efficiently with minimal back-and-forth

---

This command maximizes efficiency by preserving context space for high-level management while agents handle detailed investigation and implementation work.
