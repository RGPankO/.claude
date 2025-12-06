# Debug Command - First Principles Debugging

**Context**: {{prompt}}

---

## CRITICAL DEBUGGING MINDSET

**🚨 YOU ARE HERE BECAUSE NORMAL DEBUGGING FAILED**
- We've been running in circles
- Our assumptions are likely WRONG
- We need to question EVERYTHING
- We need EVIDENCE, not guesses

---

## STEP 1: STOP AND ASSESS (DO NOT SKIP)

Before proceeding, answer these questions explicitly:

### What Do We Actually Know vs. Assume?
- **KNOWN (verified by logs/tests)**: List only facts confirmed by evidence
- **ASSUMED (not verified)**: List everything we think is true but haven't confirmed
- **UNKNOWN (missing info)**: List what we don't know but need to know

### What Is the Actual Problem?
- **Symptom**: What behavior are we seeing?
- **Expected**: What should happen instead?
- **Delta**: What's the exact difference between actual and expected?

### What Have We Already Tried?
- List all attempted fixes and their results
- Identify patterns in what didn't work
- Note any conflicting evidence or unexpected behavior

---

## STEP 2: DEPENDENCY AND TIME-CHAIN ANALYSIS

**🔍 CRITICAL: Trace the execution path from START to ERROR**

### Execution Flow Questions:
1. **What triggers this code path?** (User action? API call? Page load? Cron job?)
2. **What runs BEFORE this code?** (List in chronological order)
3. **What does this code DEPEND ON?** (Environment vars? Database state? API responses? Browser state?)
4. **What runs AFTER this code?** (What consumes its output?)
5. **Where could the chain break?** (Each dependency is a potential failure point)

### Dependency Checklist:
- [ ] Environment variables loaded and correct?
- [ ] Database schema matches code expectations?
- [ ] External APIs responding correctly?
- [ ] File system paths exist and accessible?
- [ ] Browser state (localStorage, cookies, session)?
- [ ] Network requests completing successfully?
- [ ] Previous async operations completed?
- [ ] Required modules/packages imported correctly?

---

## STEP 3: INSTRUMENTATION STRATEGY

**🎯 GOAL: Make the invisible VISIBLE**

### Required Logging Points (Add ALL of these):

#### 1. Entry/Exit Logging
```javascript
// EVERY function in the suspect path needs this:
console.log('🔵 [FunctionName] ENTRY', {
  params: JSON.stringify(params, null, 2),
  timestamp: new Date().toISOString()
});

// ... function body ...

console.log('🟢 [FunctionName] EXIT', {
  result: JSON.stringify(result, null, 2),
  timestamp: new Date().toISOString()
});
```

#### 2. State Verification Logging
```javascript
// At critical decision points:
console.log('🔍 [Component] STATE CHECK', {
  expectedCondition: 'what we think should be true',
  actualValue: actualValue,
  matches: actualValue === expectedValue
});
```

#### 3. Dependency Verification Logging
```javascript
// Check EVERY dependency at runtime:
console.log('📦 [Module] DEPENDENCY CHECK', {
  envVar: process.env.SOME_VAR,
  dbConnection: db.isConnected,
  apiEndpoint: API_URL,
  hasRequiredData: !!requiredData
});
```

#### 4. Error Context Logging
```javascript
// Wrap suspect code:
try {
  // ... code ...
} catch (error) {
  console.error('❌ [Location] ERROR CAUGHT', {
    error: error.message,
    stack: error.stack,
    context: {
      // Include ALL relevant state here
    }
  });
  throw error; // Re-throw to see full chain
}
```

### Logging Locations to Add:

**Browser (Client-Side)**:
- [ ] Component mount/unmount
- [ ] useEffect triggers and cleanup
- [ ] API call initiation and response
- [ ] State updates (before/after)
- [ ] Event handlers (click, submit, etc.)
- [ ] Navigation/routing changes
- [ ] localStorage/sessionStorage access
- [ ] Error boundaries

**Server (API Routes/Services)**:
- [ ] API route entry (request received)
- [ ] Request body/params parsing
- [ ] Database query execution (before/after)
- [ ] External API calls (request/response)
- [ ] Business logic decision points
- [ ] Response preparation
- [ ] Error handling
- [ ] API route exit (response sent)

**Database (Prisma)**:
- [ ] Query construction
- [ ] Query parameters
- [ ] Query results (count/sample)
- [ ] Transaction start/commit/rollback
- [ ] Connection pool status

---

## STEP 4: INVESTIGATOR AGENT DELEGATION

**🤖 USE AGENTS TO ELIMINATE UNKNOWNS - DO NOT GUESS**

For EACH unknown or unverified assumption, create an investigator task:

### Investigation Task Template:
```
**Investigate**: [Specific thing to verify]
**Agent**: investigator
**Goal**: Return ONLY facts, no speculation
**Required Evidence**:
  - Source code locations
  - Actual runtime values
  - Configuration settings
  - Dependencies/imports
**Question to Answer**: [Precise question with yes/no or specific value answer]
```

### Common Investigation Tasks:

#### Investigation 1: Code Path Verification
```
Task: Trace the EXACT execution path from [trigger] to [error location]
Agent: investigator
Questions:
1. What is the call stack? (List every function called in order)
2. What files are involved? (Full paths)
3. Are there any conditional branches? (What determines which path?)
4. What are the entry points? (How does this code get invoked?)
```

#### Investigation 2: Dependency Verification
```
Task: Verify ALL dependencies for [suspect code]
Agent: investigator
Questions:
1. What external dependencies exist? (APIs, env vars, files, database)
2. Are they available at runtime? (Check existence/accessibility)
3. What are their current values? (Actual runtime values, not defaults)
4. Do the values match code expectations? (Type, format, content)
```

#### Investigation 3: State Analysis
```
Task: Analyze state at [critical point]
Agent: investigator
Questions:
1. What state does this code expect? (Props, context, database, localStorage)
2. What state actually exists? (Runtime inspection)
3. Where does this state come from? (Trace origin)
4. When is this state set? (Timeline/sequence)
```

#### Investigation 4: Configuration Verification
```
Task: Verify configuration for [feature/module]
Agent: investigator
Questions:
1. What configuration is required? (Env vars, config files, database settings)
2. Where is configuration defined? (Files, .env, database)
3. What are the actual values? (Runtime values)
4. Are there any overrides or defaults? (Precedence chain)
```

---

## STEP 5: HYPOTHESIS-DRIVEN TESTING

**🧪 SCIENTIFIC METHOD: Form hypothesis → Test → Observe → Conclude**

### For Each Hypothesis:

1. **State Hypothesis Clearly**:
   ```
   "I believe the error occurs because [specific cause]"
   ```

2. **Identify Test**:
   ```
   "To test this, I will [specific action]"
   ```

3. **Predict Outcome**:
   ```
   "If hypothesis is correct, I expect to see [specific result]"
   "If hypothesis is wrong, I expect to see [different result]"
   ```

4. **Run Test**:
   - Add necessary logging
   - Execute minimal reproduction
   - Observe actual output
   - Record results

5. **Analyze Results**:
   - Did outcome match prediction?
   - What new information did we learn?
   - What assumptions were confirmed/rejected?
   - What new questions emerged?

6. **Update Mental Model**:
   - Revise understanding based on evidence
   - Eliminate disproven theories
   - Form new hypotheses if needed

---

## STEP 6: USER-ASSISTED VERIFICATION

**👤 INVOLVE USER FOR MANUAL VERIFICATION**

When code alone can't verify something, create explicit testing instructions:

### Manual Test Template:
```
**Test ID**: [Unique identifier]
**Goal**: [What we're trying to verify]
**Steps**:
1. [Exact action to take]
2. [What to observe]
3. [Where to look (browser console, network tab, etc.)]

**Expected Result**: [What should happen if working correctly]
**Report Back**: [Specific information to provide]
```

### Example Manual Tests:

#### Browser State Test:
```
**Test**: Verify localStorage state during error
**Steps**:
1. Open browser DevTools (F12)
2. Go to Application → Local Storage
3. Trigger the error (click X, navigate to Y)
4. Screenshot the localStorage contents
5. Check Console tab for any errors

**Expected**: Should see [specific keys/values]
**Report**:
  - Screenshot of localStorage
  - Console error messages (exact text)
  - Network tab status (any failed requests?)
```

#### Network Request Test:
```
**Test**: Verify API request/response
**Steps**:
1. Open DevTools → Network tab
2. Filter by Fetch/XHR
3. Trigger the action
4. Click on the API request
5. Copy Request Headers, Request Payload, Response

**Expected**: Status 200, response contains [expected data]
**Report**:
  - Request URL and method
  - Request payload (full JSON)
  - Response status and body (full JSON)
  - Any error messages
```

#### Database State Test:
```
**Test**: Verify database state before/after operation
**Steps**:
1. Run: npx prisma studio
2. Open [TableName] table
3. Note current state (screenshot or copy data)
4. Trigger the operation
5. Refresh Prisma Studio
6. Note new state

**Expected**: [Specific changes to data]
**Report**:
  - Before state (data snapshot)
  - After state (data snapshot)
  - Any unexpected changes
```

---

## STEP 7: SYSTEMATIC ELIMINATION

**🎯 BINARY SEARCH DEBUGGING: Divide and Conquer**

### Isolation Strategy:

1. **Identify Boundaries**:
   - Where does the system work correctly? (Last known good point)
   - Where does it fail? (First known failure point)

2. **Test Midpoint**:
   - Add verification at midpoint between good and bad
   - Determine if midpoint is good or bad
   - Narrow the search space

3. **Repeat Until Isolated**:
   - Keep halving the search space
   - Eventually isolate to single function/line

### Component Isolation:
```javascript
// Test each component in isolation:

// 1. Stub out dependencies
const mockDependency = () => 'mock-value';

// 2. Call function with known inputs
const result = suspectFunction(knownInput, mockDependency);

// 3. Log result
console.log('🧪 ISOLATION TEST', {
  input: knownInput,
  expectedOutput: expectedResult,
  actualOutput: result,
  matches: result === expectedResult
});

// 4. If this passes, problem is NOT in this function
// 5. If this fails, problem IS in this function
```

---

## STEP 8: QUESTIONS TO ASK (DO NOT PROCEED WITHOUT ANSWERS)

**❓ FORCE EXPLICIT ANSWERS - NO ASSUMPTIONS**

### About the Code:
- [ ] What is the EXACT file path and line number where the error occurs?
- [ ] What function is executing when the error happens?
- [ ] What are the EXACT parameter values at the time of error?
- [ ] What is the EXACT error message (not paraphrased)?
- [ ] What is the full stack trace?

### About the Environment:
- [ ] What environment is this? (dev/staging/production/browser/server)
- [ ] What version of Node/browser?
- [ ] What are the environment variable values?
- [ ] What is the database state?
- [ ] Are there any recent changes to dependencies?

### About the Data:
- [ ] What is the EXACT input data?
- [ ] What format is it in? (string/number/object/array)
- [ ] Where does this data come from?
- [ ] Is the data valid/complete?
- [ ] Does the data match the expected schema?

### About the Flow:
- [ ] What triggers this code?
- [ ] Does it happen every time or intermittently?
- [ ] What ran immediately before this?
- [ ] What is supposed to run after this?
- [ ] Are there any race conditions possible?

### About the History:
- [ ] When did this last work correctly?
- [ ] What changed since then?
- [ ] Have we seen this error before?
- [ ] Are there similar working examples?
- [ ] What's different about the failing case?

---

## STEP 9: EXECUTION PROTOCOL

**📋 SYSTEMATIC EXECUTION - FOLLOW IN ORDER**

### Phase 1: Information Gathering (NO CODE CHANGES YET)
1. [ ] Complete STEP 1 assessment (known/assumed/unknown)
2. [ ] Map execution flow and dependencies (STEP 2)
3. [ ] Launch investigator agents for all unknowns (STEP 4)
4. [ ] Wait for ALL agent reports before proceeding
5. [ ] Update known/assumed/unknown based on findings

### Phase 2: Instrumentation
1. [ ] Add ALL logging points from STEP 3
2. [ ] Commit instrumentation separately (if user requests)
3. [ ] Test that logs appear correctly
4. [ ] Create manual test instructions for user (STEP 6)

### Phase 3: Evidence Collection
1. [ ] Run code with instrumentation
2. [ ] Collect console logs (browser AND server)
3. [ ] Execute manual tests with user
4. [ ] Review all evidence collected
5. [ ] Identify contradictions or surprises

### Phase 4: Hypothesis Formation
1. [ ] Based on evidence, form specific hypothesis
2. [ ] Design test for hypothesis (STEP 5)
3. [ ] Predict expected outcomes
4. [ ] Execute test
5. [ ] Analyze results

### Phase 5: Solution Implementation
1. [ ] ONLY after hypothesis confirmed by evidence
2. [ ] Implement minimal fix
3. [ ] Keep instrumentation in place
4. [ ] Test fix with user
5. [ ] Verify fix addresses root cause (not just symptom)

### Phase 6: Cleanup
1. [ ] Remove debug logging
2. [ ] Document root cause and fix
3. [ ] Update documentation if needed
4. [ ] Commit final solution

---

## DEBUGGING PRINCIPLES TO REMEMBER

1. **Evidence Over Intuition**: Trust logs, not hunches
2. **Verify Every Assumption**: What you "know" might be wrong
3. **Isolate Variables**: Change one thing at a time
4. **Reproduce Reliably**: If you can't reproduce it, you can't fix it
5. **Question Everything**: Especially the things you're "certain" about
6. **Make It Visible**: If you can't see it, you can't debug it
7. **Follow the Data**: Track values through the entire flow
8. **Understand, Don't Guess**: Know WHY the fix works
9. **Document Findings**: Future you will thank present you
10. **Take Breaks**: Fresh eyes catch what tired eyes miss

---

## ANTI-PATTERNS TO AVOID

**🚫 DO NOT:**
- Make multiple changes at once
- Assume "this couldn't be the problem"
- Skip logging "obvious" code paths
- Trust caches (browser, build, database)
- Believe "this code hasn't changed"
- Fix symptoms instead of root causes
- Proceed with unknowns unresolved
- Guess at solutions without evidence
- Delete useful error messages
- Trust your memory over logs

---

## OUTPUT REQUIREMENTS

Your response to this debug command MUST include:

1. **Assessment Summary** (STEP 1 completed):
   - Known facts (with evidence source)
   - Assumptions (explicitly flagged)
   - Unknowns (what we need to find out)

2. **Execution Flow Map** (STEP 2 completed):
   - Trigger → Function chain → Error location
   - Dependencies identified
   - Potential failure points marked

3. **Investigation Plan**:
   - List of investigator agent tasks (STEP 4)
   - Each with specific question to answer
   - Launch all agents in parallel

4. **Instrumentation Code**:
   - Exact logging to add (STEP 3)
   - File locations for each log
   - What each log should reveal

5. **Manual Test Instructions** (STEP 6):
   - Step-by-step user testing procedures
   - What to observe and report
   - Expected vs actual outcomes

6. **Next Steps**:
   - What we'll do when agents return
   - What evidence we need from user
   - How we'll proceed based on findings

---

## QUICK REFERENCE

### Reproduce First
Never debug what you can't reproduce. Create minimal reproduction case, document exact steps.

### Git Bisect (Find Breaking Commit)
```bash
git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>
# Git will checkout commits, you test each, mark good/bad
```

### Common Bug Categories

| Category | Symptoms | Debug Approach |
|----------|----------|----------------|
| Logic Errors | Wrong results, off-by-one | Add assertions, trace execution |
| State Issues | Race conditions, stale data | Log state changes, use immutable data |
| Async Issues | Unhandled promises, timeouts | Log async flows, check promise chains |
| Type Errors | null/undefined access | Add type checks, use TypeScript |
| Integration | API mismatches, config issues | Validate inputs/outputs at boundaries |

### Useful Commands
```bash
lsof -i :3000              # Find process using port
netstat -ano | findstr :3000  # Windows equivalent
watch -n 1 'tail -20 app.log' # Monitor log file
```

---

## REMEMBER

**You are in debug mode because normal approaches failed.**
**Question your assumptions. Gather evidence. Proceed methodically.**
**The user is your partner in this investigation - ask for help.**

Now proceed with the systematic debugging of: {{prompt}}
