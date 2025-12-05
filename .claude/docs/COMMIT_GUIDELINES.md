# Commit Guidelines

Complete guide for the `/commit` command. The command file loads this doc for detailed instructions.

## Supported Arguments

- `/commit` - Commit all changes with smart grouping
- `/commit [instructions]` - With specific instructions:
  - **Exclusion**: "exclude X", "but not X", "without X"
  - **Inclusion only**: "only X", "just X"
  - **Custom message**: "with message 'X'"
  - **File-specific**: List specific file paths
  - **Scope**: "frontend only", "backend changes"
  - **Mixed**: "only frontend but exclude tests"

## Analysis Phase

Run these commands first:
```bash
git status --porcelain
git diff --name-only
git ls-files --others --exclude-standard
```

**Categorize changes by:**
- **Backend/API**: Express routes, database scripts, server logic
- **Frontend/UI**: Components, styles, user interface
- **Database**: Schema updates, migrations
- **Bug Fixes**: Error handling, calculation fixes
- **Documentation**: CLAUDE.md, README, guides
- **Configuration**: Package.json, env configs
- **Features**: New functionality, enhancements

## Logical Grouping Rules

**Group Together (same commit):**
- Related files implementing a single feature
- Files that depend on each other
- Bug fix + its test
- Component + styles + tests

**Separate Commits:**
- Bug fixes vs new features
- Frontend vs backend changes
- Database migrations vs application code
- Documentation vs project code
- Unrelated features
- Refactoring vs new functionality

## Commit Message Format

```
<Type>: <Clear summary in imperative mood>

<Optional detailed description>
- Key change 1
- Key change 2
```

**Types:** `Add`, `Fix`, `Update`, `Remove`, `Refactor`, `Implement`, `Create`

**IMPORTANT:** Do NOT add promotional messages or signature blocks. Keep professional.

## Staging Strategy

```bash
git add <specific files for this commit>
git commit -m "$(cat <<'EOF'
<commit message>
