---
argument-hint: [instructions] (optional: "exclude X", "only Y", "with message 'Z'", specific files)
description: Smart commit with logical grouping and optional user instructions
---

# Smart Commit Command

## Purpose
Analyze the current git state and create logical, well-structured commits with meaningful messages. Split changes by type/purpose instead of committing everything at once.

## Command Syntax
- `/commit` - Commit all appropriate changes with smart grouping
- `/commit [instructions]` - Commit with specific user instructions

## User Instructions Processing

**Arguments received:** `$ARGUMENTS`

If `$ARGUMENTS` is provided, interpret it as specific instructions and apply throughout the commit process:

**Supported instruction patterns:**
- **Exclusion**: "exclude X", "but not X", "without X"
- **Inclusion only**: "only X", "just X", "include only X"
- **Custom message**: "with message 'X'", "message: X"
- **File-specific**: List specific file paths to commit
- **Scope**: "frontend only", "backend changes", "bug fixes only"
- **Mixed**: Combine patterns like "only frontend but exclude tests"

**Processing logic:**
1. Parse `$ARGUMENTS` for instruction keywords
2. Override default file selection based on instructions
3. Apply custom commit messages if specified
4. Maintain logical grouping within the filtered scope

## Command Instructions

When the user asks you to commit changes, follow this systematic approach:

### 1. Analysis Phase
```bash
git status --porcelain
git diff --name-only
git ls-files --others --exclude-standard
```

**Categorize changes by:**
- **Backend/API**: Express routes, database scripts, server logic
- **Frontend/UI**: Public JS/CSS, components, user interface
- **Database**: Schema updates, migration scripts, data fixes
- **Bug Fixes**: Error handling, calculation fixes, API issues
- **Documentation**: CLAUDE.md, README updates, guides
- **Configuration**: Package.json, environment configs (exclude personal settings)
- **Features**: New functionality, enhancements, user-facing improvements

### 2. Logical Grouping Rules

**Group Together (same commit):**
- Related files that implement a single feature
- Files that depend on each other for functionality
- Bug fix + test for that bug
- Component + its styles + its tests

**Separate Commits:**
- Bug fixes vs new features
- Frontend changes vs backend changes
- Database migrations vs application code
- CLAUDE.md updates vs project code changes
- Different unrelated features
- Refactoring vs new functionality

### 3. Commit Message Format

Use this template:
```
<Type>: <Clear summary in imperative mood>

<Optional detailed description>
- Key change 1
- Key change 2
- Key change 3

<Technical notes if complex>
```

**IMPORTANT:** Do NOT add promotional messages, attribution lines, or signature blocks to commit messages. Keep commit messages professional and focused on the actual changes.

**Types:** `Add`, `Fix`, `Update`, `Remove`, `Refactor`, `Implement`, `Create`

### 4. Staging Strategy

**For each logical group:**
```bash
git add <specific files for this commit>
git commit -m "$(cat <<'EOF'
<commit message here>
EOF
)"
```

**Never stage:**
- Personal config files (.claude/settings.local.json)
- IDE-specific files
- Temporary/cache files
- Environment-specific configs (.env files)
- Gitignored directories (node_modules, backups, etc.)

### 5. Commit Sequence Examples

**Example 1: Trading Feature + Database**
1. `Add database migration for purchase tracking` (scripts/)
2. `Implement 24-hour lock trading logic` (routes/trades.js)
3. `Update frontend trade interface` (public/app.js)
4. `Update project documentation` (CLAUDE.md)

**Example 2: Suggestions System**
1. `Add suggestions table and migration script` (database)
2. `Implement suggestions API endpoints` (backend)
3. `Add suggestions modal and UI` (frontend)
4. `Fix rate limiting edge cases` (bug fixes)

### 6. Quality Checks

Before each commit, verify:
- Commit contains logically related changes
- Message clearly explains what and why
- No unrelated files included
- No personal/local config files
- Changes are complete (not half-implemented)

### 7. Final Report

After committing, provide:
- List of commits created with summaries
- Files changed in each commit
- Any files excluded and why
- Current git status

## Usage Examples

**User says:** "/commit"
**Claude response:** Analyzes git status → Creates 2-4 logical commits → Reports what was done

**User says:** "/commit exclude level editor changes"
**Claude response:** Analyzes git status → Excludes level editor files → Creates commits for remaining changes

**User says:** "/commit only bug fixes"
**Claude response:** Identifies bug fix files → Creates targeted commits only for fixes → Leaves other changes unstaged

**User says:** "/commit with message 'Implement user authentication'"
**Claude response:** Groups auth-related files → Uses custom message → Commits other changes separately

**User says:** "/commit client/js/game.js server/game-logic.js"
**Claude response:** Commits only specified files → Ignores all other changes

**User says:** "/commit frontend only but exclude tests"
**Claude response:** Commits frontend files → Skips test files → Leaves backend changes unstaged

---

This command ensures clean git history with logical commit boundaries and professional commit messages.