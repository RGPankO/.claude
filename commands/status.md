---
argument-hint: [verbose | brief | focus:area] (optional: show more/less detail or focus on specific area)
description: Get comprehensive project status overview including git, processes, tests, and todos
---

# Status Command

## Purpose
Provide a quick, comprehensive overview of the current project state including git status, running processes, test results, build status, and active todos.

## Command Syntax
- `/status` - Standard status overview
- `/status verbose` - Detailed status with more information
- `/status brief` - Minimal status summary
- `/status focus:git` - Focus on git status
- `/status focus:tests` - Focus on test status
- `/status focus:build` - Focus on build status

## User Instructions Processing

**Arguments received:** `$ARGUMENTS`

Parse `$ARGUMENTS` for modifiers:
- `verbose`: Show detailed information
- `brief`: Show minimal summary only
- `focus:X`: Focus on specific area (git, tests, build, todos, deps)

## Command Instructions

Execute these checks in parallel when possible for faster results:

### 1. Git Status Check
```bash
# Get current branch
git branch --show-current

# Check for uncommitted changes
git status --short

# Check for unpushed commits
git log --oneline @{u}.. 2>/dev/null | head -5

# Check for stashes
git stash list | head -3

# Show recent commits
git log --oneline -5
```

### 2. Process and Server Status
```bash
# Check for running dev servers (common ports)
lsof -i :3000,3001,4000,5000,5173,8000,8080 2>/dev/null | grep LISTEN

# Check for running node processes
ps aux | grep -E "node|npm|yarn|pnpm" | grep -v grep

# Check for database processes
ps aux | grep -E "postgres|mysql|mongo|redis" | grep -v grep
```

### 3. Test Status
```bash
# Check if tests exist and last run results
if [ -f "package.json" ]; then
  # Check for test script
  grep -q '"test"' package.json && echo "Test script available"

  # Check for recent test results or coverage
  if [ -d "coverage" ]; then
    ls -la coverage/ | head -5
  fi
fi

# Python projects
if [ -f "pytest.ini" ] || [ -f "setup.cfg" ] || [ -f "pyproject.toml" ]; then
  echo "Python test configuration found"
fi
```

### 4. Build Status
```bash
# Check for build artifacts
if [ -d "dist" ] || [ -d "build" ]; then
  echo "Build directory found:"
  ls -la dist/ 2>/dev/null || ls -la build/ 2>/dev/null | head -5
fi

# Check last build time
find . -type f -name "*.js" -o -name "*.ts" -newer dist 2>/dev/null | head -5
```

### 5. TODO Status
```bash
# Find TODOs in code
grep -r "TODO\|FIXME\|HACK\|XXX" --include="*.js" --include="*.ts" --include="*.jsx" --include="*.tsx" --include="*.py" --include="*.java" --include="*.go" . 2>/dev/null | head -10

# Check for todo list file
if [ -f ".todos" ] || [ -f "TODO.md" ]; then
  echo "TODO file found"
  head -10 .todos 2>/dev/null || head -10 TODO.md 2>/dev/null
fi
```

### 6. Dependency Status
```bash
# Node.js projects
if [ -f "package.json" ]; then
  # Check if node_modules exists
  [ -d "node_modules" ] && echo "Dependencies installed" || echo "Dependencies not installed"

  # Check for outdated packages (quick check)
  npm outdated 2>/dev/null | head -5 || yarn outdated 2>/dev/null | head -5
fi

# Python projects
if [ -f "requirements.txt" ] || [ -f "Pipfile" ] || [ -f "pyproject.toml" ]; then
  echo "Python dependency file found"
fi
```

### 7. Environment Status
```bash
# Check for environment files
[ -f ".env" ] && echo ".env file present"
[ -f ".env.local" ] && echo ".env.local file present"
[ -f ".env.example" ] && echo ".env.example file present"

# Check Node version if applicable
node --version 2>/dev/null
npm --version 2>/dev/null || yarn --version 2>/dev/null

# Check Python version if applicable
python --version 2>/dev/null || python3 --version 2>/dev/null
```

## Output Format

### Standard Output
```
PROJECT STATUS REPORT
====================

📁 Git Status
  Branch: main
  Changes: 5 files modified, 2 untracked
  Unpushed: 3 commits ahead
  Recent: "Fix: user authentication bug" (2 hours ago)

🚀 Processes
  Dev Server: Running on port 3000
  Database: PostgreSQL active
  Background: 2 worker processes

✅ Tests
  Last Run: 45/45 passed (coverage: 78%)
  Framework: Jest configured

🔨 Build
  Last Build: 10 minutes ago
  Size: dist/ (2.4 MB)
  Status: Up to date

📝 TODOs
  Code TODOs: 8 found
  High Priority: 2
  In Progress: 3

📦 Dependencies
  Status: All installed
  Outdated: 3 packages (minor updates)
  Security: No vulnerabilities

🔧 Environment
  Node: v18.17.0
  Package Manager: npm 9.8.1
  Config: .env present
```

### Brief Output
```
STATUS: ✅ Ready
Git: main (5 changes) | Server: Running :3000 | Tests: 45/45 ✅ | Build: Current
```

### Verbose Output
Include additional details like:
- Full file lists for changes
- All running processes with PIDs
- Complete test output
- Full dependency list
- All environment variables (names only)

## Error Handling

Handle common issues gracefully:
- No git repository: Skip git section
- No package.json: Skip Node.js checks
- No tests configured: Note "No tests found"
- Permission errors: Note "Permission denied for X"

## Performance Considerations

- Run checks in parallel where possible
- Cache results for subsequent calls within 60 seconds
- Limit output lines to prevent overwhelming display
- Use `head` to limit long outputs
- Skip slow checks in brief mode

## Usage Examples

**User says:** "/status"
**Result:** Full standard status report

**User says:** "/status brief"
**Result:** One-line summary of key metrics

**User says:** "/status focus:git"
**Result:** Detailed git status with all branches, stashes, and recent history

**User says:** "/status verbose"
**Result:** Comprehensive status with all details

---

This command provides developers with instant insight into their project state without running multiple manual commands.