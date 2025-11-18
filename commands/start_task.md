# Start Task Command

## Purpose
Universal task management command that works across any project. Quickly focus on specific work with context loading, todo creation, and clear next steps.

## Command Format
```
/start_task <task description>
```

## Command Instructions

When the user invokes this command, Claude should:

### 1. Task Context Setup
**Create focused work session:**

```markdown
**🎯 TASK FOCUS**: {task description from user}
**📅 SESSION START**: {current date/time}
**📁 PROJECT**: {detect project name from directory}
```

### 2. Project Context Discovery
**Auto-detect project type and load relevant context:**

```bash
# Quick project assessment
pwd
ls -la | head -10
git status --porcelain 2>/dev/null || echo "Not a git repo"
```

**Look for common project files:**
- `package.json` → Node.js project
- `README.md` → Project documentation
- `CLAUDE.md` → Claude-specific instructions
- `.kiro/specs/` → Task specifications
- `tasks/` → Task lists
- Language-specific files (`.py`, `.rs`, `.go`, etc.)

### 3. Task Documentation Search
**Find relevant task files:**
- Search for task lists, specifications, or documentation
- Look for keywords in user's task description
- Check for project-specific task tracking files
- Read any existing todo files or task management

### 4. Work Environment Assessment
**Check project readiness:**
```bash
# Development environment check
npm test --passWithNoTests --silent 2>/dev/null || echo "No npm tests"
python -m pytest --version 2>/dev/null || echo "No pytest"
cargo check --quiet 2>/dev/null || echo "No Rust project"
```

### 5. Create Focused Todo List
**Based on task description, create actionable items:**
- Break down the task into 3-5 immediate steps
- Identify any prerequisites or dependencies
- Note potential blockers or unknowns
- Set clear success criteria

### 6. Provide Ready-to-Work Summary

```markdown
## 🚀 Ready to Work!

### 📋 Task: {user's task description}

**Project Type**: {detected project type}
**Current Status**: {git status summary}

### ✅ Next Steps:
1. {immediate first action}
2. {logical next step}
3. {follow-up action}
4. {validation/testing step}

### 📁 Key Areas to Focus:
- {relevant files or directories}
- {configuration or documentation}
- {testing or validation approach}

### 🛠️ Suggested Commands:
- {development command if applicable}
- {test command if applicable}
- {build/lint command if applicable}

### 📖 References Found:
- {link to relevant documentation}
- {task files or specifications}

**💭 Notes**: {any important context or considerations}
```

## Auto-Detection Logic

### Project Type Detection
```javascript
// Detection priority order:
if (package.json exists) → "Node.js/JavaScript project"
else if (Cargo.toml exists) → "Rust project"
else if (requirements.txt or pyproject.toml) → "Python project"
else if (go.mod exists) → "Go project"
else if (.gitignore or README) → "General development project"
else → "Unknown project type"
```

### Task Context Clues
```javascript
// Common task keywords:
"bug" → Focus on debugging, testing, reproduction
"feature" → Focus on implementation, requirements, testing
"refactor" → Focus on code organization, tests, documentation
"test" → Focus on testing framework, coverage, validation
"docs" → Focus on documentation, README, guides
"deploy" → Focus on build, configuration, CI/CD
"setup" → Focus on installation, configuration, dependencies
```

## Usage Examples

### Example 1: Bug Fix
```
User: /start_task fix authentication bug in login system
Claude:
🎯 TASK FOCUS: Fix authentication bug in login system
📁 PROJECT: MyWebApp (Node.js/Express)

## 🚀 Ready to Work!

### 📋 Task: Fix authentication bug in login system
**Project Type**: Node.js/Express application
**Current Status**: 3 modified files, 1 untracked

### ✅ Next Steps:
1. Reproduce the authentication bug
2. Check authentication middleware and routes
3. Review recent changes to login system
4. Write test to verify fix works

### 📁 Key Areas to Focus:
- src/auth/ (authentication logic)
- routes/login.js (login endpoints)
- tests/auth.test.js (authentication tests)

### 🛠️ Suggested Commands:
- npm run dev (start development server)
- npm test auth (run authentication tests)
- npm run lint (check code style)

### 📖 References Found:
- README.md (project setup)
- docs/authentication.md (auth documentation)

💭 Notes: Check if bug is related to recent middleware changes
```

### Example 2: New Feature
```
User: /start_task implement user profile dashboard
Claude:
🎯 TASK FOCUS: Implement user profile dashboard
📁 PROJECT: ReactApp (React/TypeScript)

## 🚀 Ready to Work!

### 📋 Task: Implement user profile dashboard
**Project Type**: React/TypeScript application
**Current Status**: Clean working directory

### ✅ Next Steps:
1. Design dashboard component structure
2. Create user profile API endpoints
3. Implement dashboard UI components
4. Add routing and navigation
5. Write component tests

### 📁 Key Areas to Focus:
- src/components/ (React components)
- src/pages/ (page-level components)
- src/api/ (API integration)
- src/types/ (TypeScript definitions)

### 🛠️ Suggested Commands:
- npm start (development server)
- npm test (component tests)
- npm run type-check (TypeScript validation)

### 📖 References Found:
- Design mockups in docs/designs/
- API specification in docs/api.md

💭 Notes: Consider responsive design for mobile devices
```

### Example 3: Generic Task
```
User: /start_task optimize database queries
Claude:
🎯 TASK FOCUS: Optimize database queries
📁 PROJECT: BackendAPI (Python/Django)

## 🚀 Ready to Work!

### 📋 Task: Optimize database queries
**Project Type**: Python/Django application
**Current Status**: 2 modified files

### ✅ Next Steps:
1. Profile current query performance
2. Identify slow queries in logs
3. Add database indexes where needed
4. Optimize ORM queries for N+1 problems
5. Measure performance improvements

### 📁 Key Areas to Focus:
- models.py (database models)
- views.py (query logic)
- requirements.txt (profiling tools)

### 🛠️ Suggested Commands:
- python manage.py runserver (development)
- python manage.py test (run tests)
- python manage.py shell (Django shell)

### 📖 References Found:
- Django documentation
- Performance monitoring setup

💭 Notes: Consider adding django-debug-toolbar for query analysis
```

## Fallback Behavior

### No Project Context Found:
```markdown
🎯 TASK FOCUS: {user task}
📁 PROJECT: {current directory name}

## 🚀 Ready to Work!

### 📋 Task: {user's task description}
**Project Type**: Unknown/General
**Current Status**: {basic directory listing}

### ✅ Next Steps:
1. Understand the task requirements
2. Explore the codebase structure
3. Identify relevant files and dependencies
4. Plan implementation approach

### 📁 Key Areas to Explore:
- Current directory structure
- Configuration files
- Documentation or README files

### 🛠️ General Commands:
- ls -la (explore directory)
- find . -name "*.{ext}" (find specific files)
- git log --oneline (recent changes)

💭 Notes: Run additional commands to understand project structure
```

## Integration Features

### Works with existing commands:
- Can reference todo lists created by this command
- Integrates with `/commit` command for progress tracking
- Respects project-specific `.claude/` configurations

### Adaptable to any project:
- No hardcoded project names or paths
- Generic enough for any language/framework
- Focuses on work process, not specific tools

---

This universal command helps establish work focus and context for any development task across any project type.