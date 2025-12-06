# Testing Request Guide

Guide for creating testing request documents. The tester's ONLY job is to use a browser and optionally watch terminal output. Everything else is the developer's responsibility.

## Core Philosophy

**The tester is NOT a developer.** They should be able to follow the test like a recipe - no thinking, no problem-solving, minimal setup.

| Developer's Job (Claude) | Tester's Job |
|--------------------------|--------------|
| Run migrations | Open browser |
| Set up config files | Click links/buttons |
| Verify environment is ready | Fill in forms (with EXACT values provided) |
| Prepare everything beforehand | Register account (if testing registration) |
| Determine the app URL | Login with provided credentials (if needed) |
| | Watch browser console (if asked) |
| | Run `npm run dev` and watch terminal (if asked) |
| | Report what they see |

**Terminal monitoring option**: If the developer wants the tester to monitor server logs, the developer must:
1. Kill any running server process first
2. Tell tester exactly what command to run (e.g., `npm run dev`)
3. Tell tester exactly what to look for in terminal output
4. Provide specific patterns/keywords to watch for

**Before writing a test request, the developer MUST:**
1. Verify the server is running (or prepare terminal monitoring)
2. Verify the database is migrated
3. Create any needed test accounts (unless testing registration)
4. Create any needed config files
5. Verify all URLs are accessible
6. Determine the base URL for the app

## Document Structure

### Header

```markdown
# Testing Request: [Feature Name]

**Date**: [YYYY-MM-DD]
**Prepared by**: Claude
**App URL**: [base URL - e.g., http://localhost:3000]
**Server Status**: Running (verified) / Tester will start
```

### Pre-Test Verification (Developer Checklist)

This section is for the DEVELOPER to complete before handing off:

```markdown
## Pre-Test Verification (Completed by Developer)

- [x] Server running at [APP_URL] OR instructions for tester to start it
- [x] Database migrated
- [x] Test account created: username `tester`, password `Test123!` (if needed)
- [x] Config file created at [path] (if needed)
- [x] Verified [URL] loads correctly
```

### Starting State & Auth Handling

Handle all authentication scenarios explicitly:

```markdown
## Starting State

**App URL**: [base URL]

### If you are LOGGED IN (you see username/avatar in header):
1. Click the user menu (top-right)
2. Click "Logout"
3. You should now see the login page

### If you are LOGGED OUT (you see "Sign In" link):
Good - proceed to the tests below.

### If you need to LOG IN for this test:
1. Go to [APP_URL]/signin
2. Username: `tester`
3. Password: `Test123!`
4. Click "Sign In"
5. You should land on the dashboard
```

### Test Cases

Each test case must be EXHAUSTIVELY specific:

```markdown
## Test Cases

### TC-001: [Test Name]

**Starting Point**: [APP_URL]/some-page

**Pre-conditions**:
- You should see [exact description of what's on screen]
- If not, STOP and report

**Steps**:
1. Look at the page. You should see a blue button labeled "Create New"
2. Click the "Create New" button
3. A form appears. You should see these fields:
   - "Name" (text input, empty)
   - "Description" (text area, empty)
   - "Submit" button (gray, at bottom)
4. In the "Name" field, type exactly: `Test Item One`
5. In the "Description" field, type exactly: `This is a test description`
6. Click the "Submit" button

**Expected Result**:
- Page redirects to [APP_URL]/items/[some-id]
- You see "Test Item One" as the page title
- You see "This is a test description" in the description area
- A green "Success" toast appears in top-right corner (disappears after 3 seconds)

**What to Record**:
- Did the redirect happen? YES/NO
- What URL are you on now? [write the full URL]
- Is "Test Item One" visible? YES/NO
- Did you see the green toast? YES/NO

**On Fail**: STOP - describe what you see instead
```

### Registration Testing

If testing registration flow:

```markdown
### TC-001: Register New Account

**Starting Point**: [APP_URL]/signup

**Pre-conditions**:
- You should see a registration form
- If you see a dashboard instead, you're logged in - logout first (see Starting State)

**Steps**:
1. In "Username" field, type exactly: `testuser123`
2. In "Email" field, type exactly: `testuser123@test.com`
3. In "Password" field, type exactly: `Test123!`
4. In "Confirm Password" field, type exactly: `Test123!`
5. Click the "Sign Up" button

**Expected Result**:
- Success message appears OR redirect to dashboard/login
- No error messages

**What to Record**:
- Registration succeeded? YES/NO
- Where did you land after? [URL]
- Any error message? [copy exact text]
```

### Browser Console Monitoring (When Required)

If browser console monitoring is needed, be specific:

```markdown
## Browser Console Monitoring

**Before starting tests**:
1. Open browser Developer Tools (press F12)
2. Click the "Console" tab
3. Click the "Clear" button (🚫 icon) to clear existing messages

**During tests**:
- Watch for RED error messages
- Ignore yellow warnings unless they mention [specific keyword]

**What to record**:
- Any red error message: copy the FIRST line only
- Count of red errors: [number]
```

### Terminal/Server Log Monitoring (When Required)

If terminal monitoring is needed, the developer must first kill any running server, then provide these instructions:

```markdown
## Terminal Monitoring

**Starting the server** (you will run this):
1. Open a terminal/command prompt in the project root folder
2. Run: `npm run dev`
3. Wait until you see: `Server running on [APP_URL]`
4. Keep this terminal window visible during testing

**What to watch for in terminal**:
- Lines containing `ERROR` (red text)
- Lines containing `[specific keyword]`
- Any stack traces (multiple lines starting with "at ...")

**What to record**:
- Copy any ERROR lines you see
- Note the timestamp (left side) when errors appear
- If you see `[specific pattern]`, copy that entire line

**Example of what to look for**:
```
✗ ERROR: Database connection failed    <-- RECORD THIS
  at src/db/connect.ts:45              <-- Include stack trace
✓ Price update: 50123.45               <-- Ignore (normal)
```
```

### Report Template

Provide exact template for reporting:

```markdown
## Report Template

Save to: `tools/testing/results/[feature-name]-[YYYY-MM-DD].md`

```
# Test Report: [Feature Name]

**Date**: [Today's date]
**Tester**: [Your name]

## Results

| Test | Pass/Fail | Notes |
|------|-----------|-------|
| TC-001 | | |
| TC-002 | | |

## TC-001: [Name]
- Redirect happened: YES/NO
- Final URL:
- Title visible: YES/NO
- Toast appeared: YES/NO

## TC-002: [Name]
...

## Console Errors
- Total red errors: [number]
- Error messages (first line only):
  1.
  2.

## Terminal Errors (if monitoring)
- Errors observed: YES/NO
- Error messages:
  1.
  2.

## Screenshots (if taken)
- [description]: [filename]

## Other Observations
[Anything unexpected you noticed]
```
```

## Specificity Examples

### BAD (vague)
```
**Expected**: Form submits successfully
```

### GOOD (specific)
```
**Expected**:
- Button changes to "Saving..." for 1-2 seconds
- Page redirects to /items/[id] (URL changes)
- Green toast says "Item created successfully"
- New item appears in list with name "Test Item One"
```

### BAD (requires thinking)
```
**Steps**:
1. Fill in the form with test data
```

### GOOD (no thinking required)
```
**Steps**:
1. In "Name" field, type exactly: `My Test Item`
2. In "Price" field, type exactly: `99.99`
3. Leave "Description" empty
4. Click the blue "Save" button
```

### BAD (assumes auth state)
```
## Setup
1. Login to the app
```

### GOOD (handles all auth states)
```
## Starting State

### If you are LOGGED IN:
1. Logout first (click avatar > Logout)

### If you are LOGGED OUT:
Good - proceed to tests
```

## Blocker Guidelines

```markdown
## When to STOP vs CONTINUE

**STOP IMMEDIATELY if**:
- Page shows "500 Internal Server Error"
- Page shows "Cannot connect to server"
- Login doesn't work (can't proceed without auth)
- Required button/field doesn't exist

**Continue but FLAG if**:
- Wrong redirect (went somewhere unexpected)
- Missing success message but data saved
- Console shows errors but page works
- Slow response (more than 5 seconds)

**Note but don't worry**:
- Styling looks off
- Typos in text
- Minor layout issues
```

## Developer Pre-Flight Checklist

Before creating a test request, verify:

```markdown
## Developer Pre-Flight (Do Before Writing Request)

1. [ ] Determine app URL (check server output or config)
2. [ ] Start server OR prepare terminal monitoring instructions
3. [ ] Test main URL loads
4. [ ] Database ready (run migrations if needed)
5. [ ] Create test user if needed (unless testing registration)
6. [ ] Create any config files needed
7. [ ] Verify starting page state matches what you'll describe
8. [ ] Clear any test data from previous runs if needed
```

## Remember

- **Tester = Browser + optional terminal monitoring**
- **Developer = All other setup**
- **Be so specific a child could follow it**
- **Handle both logged-in and logged-out scenarios**
- **Use [APP_URL] placeholder, fill in actual URL in header**
- **Exact values, exact URLs, exact button names**
