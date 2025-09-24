# Context Management Guide

> **Context Loading**: This file is REFERENCED, not loaded. Main Claude should understand these principles for optimal performance.

## Context Window Philosophy

The context window is your working memory. Like human working memory, it's limited and precious. Every token counts, and clarity beats completeness.

## Context Loading Hierarchy

### Always Loaded (Keep Minimal)
1. **CLAUDE.md**: Project overview and quick reference (<500 lines)
2. **Current Task**: Active files being modified
3. **Critical Context**: Recent error messages, user requirements

### Load When Needed
1. **Implementation Files**: When actively modifying
2. **Test Files**: When writing/running tests
3. **Config Files**: When changing settings

### Reference, Don't Load
1. **Documentation**: Use docs-explorer agent
2. **Large Files**: Use grep/search instead
3. **Historical Context**: Reference git history
4. **External APIs**: Keep links, not full docs

### Never Load
1. **Generated Files**: build/, dist/, node_modules/
2. **Binary Files**: images, compiled code
3. **Repetitive Code**: Similar test files, data files
4. **Archived Content**: Old versions, backups

## Context Management Strategies

### 1. Progressive Disclosure
Start with high-level understanding, drill down as needed:
```
1. Read CLAUDE.md for overview
2. Use codebase-analyzer for structure
3. Load specific files for implementation
4. Unload when done
```

### 2. Context Rotation
Keep active working set small:
```
- Load file A, B for feature X
- Complete feature X
- Unload A, B
- Load file C, D for feature Y
```

### 3. Summarization
Replace detailed content with summaries:
```
Instead of: [500 lines of code]
Use: "UserService handles authentication with JWT,
      methods: login(), logout(), refresh()"
```

### 4. Agent Delegation
Use agents to explore without loading:
```
❌ Bad: Load 10 files to understand auth
✅ Good: Ask codebase-analyzer about auth pattern
```

## Context Signals

### Signs of Context Overload
- Responses become generic
- Missing obvious connections
- Repeating previous mistakes
- Slower response times
- Confusion about requirements

### Signs of Insufficient Context
- Asking redundant questions
- Making incorrect assumptions
- Missing project conventions
- Violating established patterns
- Creating duplicate functionality

## File Loading Guidelines

### When to Load Files
✅ **Load when**:
- Actively editing the file
- Need exact implementation details
- Debugging specific issues
- Writing tests for the code
- Refactoring the module

❌ **Don't load when**:
- Just need to know if file exists
- Only checking structure
- Looking for patterns (use grep)
- Need documentation (use agent)
- Reviewing old code

### Optimal File Loading Patterns

**Single File Edit**:
```
1. Load target file
2. Make changes
3. Verify changes
4. (File auto-unloaded after response)
```

**Multi-File Feature**:
```
1. Load interface/contract file
2. Load implementation file
3. Complete feature
4. Load test file
5. Write tests
```

**Debugging Session**:
```
1. Load error context
2. Load suspected problem file
3. Fix issue
4. Load test to verify
```

## Context Preservation Techniques

### 1. Working Notes
Maintain a temporary summary of findings:
```markdown
## Current Understanding
- Auth uses JWT with refresh tokens
- Tokens stored in httpOnly cookies
- Refresh endpoint: /api/auth/refresh
- Token expiry: 15 minutes
```

### 2. Decision Log
Track important decisions:
```markdown
## Decisions Made
1. Use Redis for session storage (performance)
2. Implement soft deletes (audit requirement)
3. Add rate limiting to API (security)
```

### 3. Task Context
Keep task requirements visible:
```markdown
## Current Task
- [ ] Add password reset functionality
- [ ] Send email with reset link
- [ ] Validate token and update password
- [ ] Add rate limiting
```

## Search vs Load

### Use Search/Grep When
- Finding where something is defined
- Looking for usage patterns
- Checking if something exists
- Finding similar implementations
- Scanning for TODOs

### Load File When
- Need to understand full logic
- Making modifications
- Debugging specific behavior
- Writing detailed tests
- Refactoring code

## Context Commands

### Useful Context Management Commands
```bash
# Find without loading
grep -r "function.*auth" --include="*.js"

# Check structure without loading
find . -type f -name "*.test.js" | head -20

# Get summary without loading
wc -l src/**/*.js

# Preview without full load
head -50 src/auth/service.js
```

## Agent Context Management

### How Agents Preserve Context
1. **Isolation**: Agents work in separate context
2. **Summarization**: Return only essential findings
3. **Reference**: Point to locations vs loading code
4. **Filtering**: Provide relevant info only

### Agent vs Direct Loading
```
Task: Understand payment processing

❌ Direct Loading:
- Load 15 payment-related files
- Context becomes cluttered
- Lose focus on current task

✅ Agent Approach:
- Ask codebase-analyzer for payment flow
- Get summary and file locations
- Load only files you need to modify
```

## Context Budget

### Allocate Context Like a Budget
- **30%**: Current task files
- **20%**: Related/dependent files
- **20%**: Test files
- **20%**: Reference/documentation
- **10%**: Buffer for exploration

### Context Cost Analysis
**High Cost** (Avoid):
- Large data files
- Generated code
- Repetitive tests
- Full documentation

**Low Cost** (Prefer):
- Interfaces/contracts
- Configuration
- Small utilities
- Error messages

## Memory Management Patterns

### The Stack Pattern
Push context for tasks, pop when complete:
```
1. Push: Load auth files for auth feature
2. Work: Implement auth feature
3. Pop: Clear auth context
4. Push: Load payment files for payment feature
```

### The Cache Pattern
Keep frequently needed info in CLAUDE.md:
```
- Common commands
- Project structure
- Key conventions
- Important decisions
```

### The Index Pattern
Use CLAUDE.md as index to detailed docs:
```
For X, see: .claude/docs/X.md
Instead of embedding X in CLAUDE.md
```

## Context Anti-Patterns

### 1. The Hoarder
Loading everything "just in case"

### 2. The Amnesiac
Not maintaining any context between tasks

### 3. The Historian
Keeping old context from completed tasks

### 4. The Deep Diver
Loading entire dependency chains

### 5. The Librarian
Loading all documentation upfront

## Best Practices

1. **Start Minimal**: Load less than you think you need
2. **Clean as You Go**: Unload completed work
3. **Summarize Often**: Replace details with summaries
4. **Use Agents**: Delegate exploration to agents
5. **Reference, Don't Embed**: Link to docs vs copying them
6. **Trust the System**: Let context management be automatic
7. **Monitor Signals**: Watch for overload/underload signs

## Recovery Strategies

### When Context is Overloaded
1. Summarize current understanding
2. Clear unnecessary files
3. Restart with minimal context
4. Use agents for exploration

### When Context is Lost
1. Re-read CLAUDE.md
2. Check recent git commits
3. Review task requirements
4. Use codebase-analyzer for orientation