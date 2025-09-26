# Commit Guidelines

## Smart Commit Strategy

The `/commit` command uses intelligent grouping to create meaningful commits rather than granular file-by-file commits.

## Temporary File Handling

**File Patterns to Watch**:
- `debug-*.ext` - Debugging scripts (never commit)
- `test-*.ext` - Temporary tests (ask before committing)
- `scratch-*.ext` - Experiments (never commit)
- Files in `.tmp/` directory (auto-excluded)

**Project Integration Detection** (identify misplaced/temporary files by location and context):
- **Files in wrong location**: Implementation files in root when project structure is in `packages/`, `src/`, `app/`, etc.
- **Orphaned files**: Files that don't integrate with existing project architecture or build system
- **Missing imports/exports**: Files not referenced by any other project files
- **Standalone implementations**: Files that duplicate existing project functionality without integration
- **Out-of-structure files**: Files that bypass established project organization patterns

**Analysis Before Committing**:
- Check if file location matches project structure (`packages/`, `src/`, etc. vs root)
- Verify if file is imported/referenced by project code
- Confirm file follows project's architectural patterns
- Ensure file doesn't duplicate existing functionality without purpose

**Commit Rules**:
- Never commit anything from `.tmp/`
- Ask before committing files with temp prefixes
- **Ask user before committing files in unexpected locations**
- **Verify integration before committing standalone implementation files**
- Delete temporary files instead of committing them
- Suggest proper project location for misplaced files
- Recommend integration or removal of orphaned files

## Logical Grouping Patterns

**Feature Implementation**:
- Group related files by feature/module
- Combine component + story + type files
- Include tests with their implementation

**Documentation Updates**:
- Group doc updates with related code changes
- Separate major doc overhauls into their own commits

**Configuration Changes**:
- Group related config file updates
- Keep build/deployment configs together

## Commit Message Format

- Start with action verb (Add, Update, Fix, Remove)
- Be specific about what changed and why
- Reference issue numbers when applicable
- End with Claude Code signature and co-author attribution