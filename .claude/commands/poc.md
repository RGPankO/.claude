# PoC Command

Proof of Concept - validate technical feasibility before full implementation.

## Instructions

If you haven't read it in this session, read `.claude/docs/POC_GUIDE.md` first.

## Arguments

$ARGUMENTS - Optional: specific component to PoC, spec file path, or "check" to review results.

## Task

### Mode 1: Analyze & Propose (default, no args or spec path)

1. **Gather Context**
   - Read provided spec file OR use conversation context
   - Identify the feature/system being built

2. **Identify PoC Candidates**
   Scan for:
   - External APIs (YouTube, payment, AI services, etc.)
   - New libraries/technologies not used before
   - Integration points (OAuth, webhooks, SSO)
   - Data processing unknowns (formats, performance)
   - Browser APIs with compatibility concerns

3. **Present PoC Recommendations**
   ```
   ## PoC Candidates

   ### 1. [Component Name] - HIGH PRIORITY
   **Risk**: [Why this is uncertain]
   **PoC Goal**: [What we need to prove]
   **Approach**: [Browser HTML / Script / Integration]
   **Time Estimate**: [30min / 1hr / 2hr]

   ### 2. [Component Name] - MEDIUM PRIORITY
   ...

   ## Recommendation
   Start with [X] because [reason - usually highest dependency/risk].
   ```

4. **Ask User**
   - Which PoC to build?
   - Or let them specify something different

### Mode 2: Build PoC (specific component provided)

1. **Define Success Criteria**
   ```
   SUCCESS means:
   - [ ] [Specific measurable outcome]
   - [ ] [Another measurable outcome]
   ```

2. **Create Minimal PoC**
   - Location: `tools/tmp/poc/[name]-poc.html` or `.js/.ts`
   - Single file when possible
   - Hardcoded values OK
   - Focus on answering the question, not code quality

3. **Provide Test Instructions**
   ```
   ## How to Test

   1. [Step to run/open the PoC]
   2. [What to input]
   3. [What to observe]

   ## Expected Results
   - Success looks like: [description]
   - Failure looks like: [description]
   ```

### Mode 3: Check Results (`/poc check`)

1. **Review PoC outcome with user**
   - Did it work?
   - What did we learn?
   - Any surprises?

2. **Document & Decide**
   ```
   ## PoC Result: [Component]

   **Status**: SUCCESS / PARTIAL / FAILED

   **Findings**:
   - [Key learning]

   **Next Steps**:
   - [Proceed with full build / Try alternative / Reassess feature]
   ```

## PoC Principles

- **Minimal**: Smallest code that answers the question
- **Isolated**: No dependencies on main codebase
- **Throwaway**: Code quality doesn't matter
- **Focused**: Test ONE thing
- **Time-boxed**: 30min-2hr max

## Output Location

All PoC files go in: `tools/tmp/poc/`

This directory is gitignored - PoCs are disposable.

## Examples

**User**: `/poc`
**Action**: Analyze current spec/context, propose PoC candidates

**User**: `/poc youtube subtitle extraction`
**Action**: Build minimal PoC to test YouTube subtitle fetching

**User**: `/poc .claude/specs/article-generator.md`
**Action**: Read spec, identify and propose PoC candidates

**User**: `/poc check`
**Action**: Review PoC results, document findings, decide next steps
