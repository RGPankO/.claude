---
argument-hint: <what to test>
description: Create QA test request documents
---

# Test Command

## Instructions

If you haven't read `.claude/docs/TESTING_REQUEST_GUIDE.md` in this session, read it first.

## Arguments

$ARGUMENTS - Description of what to test. If empty, use current conversation context.

## Task

Create a test request document in `tools/testing/requests/`.

**CRITICAL**: Before writing the test request, YOU (the developer) must:

1. **Prepare the environment**:
   - Start/restart the server if needed (or prepare terminal monitoring instructions)
   - Run any pending migrations
   - Create test accounts if needed (unless testing registration)
   - Create any required config files
   - Determine the app URL from server output or config

2. **Verify the starting state**:
   - Open the relevant pages and confirm they load
   - Confirm the test account can log in (if auth required)
   - Document exactly what the tester will see

3. **Write exhaustively specific test cases**:
   - Use [APP_URL] placeholder in templates, fill actual URL in header
   - Exact text to type in each field
   - Exact button names to click
   - Exact expected results (what appears, where, what color, etc.)
   - Exact questions for tester to answer (YES/NO format)

4. **Handle authentication scenarios**:
   - Provide instructions for BOTH logged-in and logged-out states
   - Tell tester how to logout if they need to be logged out
   - Tell tester how to login if they need to be logged in
   - If testing registration, provide exact credentials to register with

**The tester's job is**:
- Open browser
- Click/type exactly what you tell them
- Register account (if testing registration flow)
- Login with provided credentials (if needed)
- Logout (if instructed)
- Watch browser console (if you ask)
- Run `npm run dev` and watch terminal output (if you ask)
- Report what they see

**The tester should NEVER**:
- Run arbitrary terminal commands (only `npm run dev` if asked)
- Edit config files
- Run migrations
- Figure out what to do next
- Make decisions about edge cases

**If you want tester to monitor terminal**: Kill the running server first, then provide exact instructions for what command to run, what to look for, and examples of output to record.

## Output

Save test request to: `tools/testing/requests/[feature-name].md`

Include:
- Header with App URL and server status
- Pre-Test Verification checklist (mark completed items)
- Starting State with auth handling (logged-in AND logged-out scenarios)
- Test Cases with exhaustive specificity
- Console/Terminal Monitoring instructions (if needed)
- Report Template with exact fields to fill
- Blocker Guidelines (STOP vs CONTINUE)
