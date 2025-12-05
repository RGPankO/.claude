# Code Principles

> **Enforcement**: All agents that write code MUST follow these principles. Read via `/start`.

## Hard Limits (Enforced)

| Rule | Limit | Rationale |
|------|-------|-----------|
| Function length | Max 50 lines | Fits on screen, single responsibility |
| Parameters | Max 4 params | Use options object for more |
| Nesting depth | Max 4 levels | Use early returns, extract functions |
| File length | Max 300 lines | Split if larger |
| Class methods | Max 10 public | Class doing too much if more |

## Code Structure

### Functions
- **One thing**: Each function does ONE thing well
- **Early returns**: Guard clauses at top, reduce nesting
- **Pure when possible**: Minimize side effects in calculations
- **Self-documenting names**: `getUserById` not `fetch` or `getData`

### Files
- **One module per file**: No monoliths
- **Clear naming**: Match content (`user.service.ts`, `auth.middleware.ts`)
- **Grouped imports**: External → Internal → Relative

### Error Handling
```javascript
// DO: Specific, informative errors
throw new Error(`User ${userId} not found in database`);

// DON'T: Swallow or generic
catch (e) { return null; }
catch (e) { throw new Error('Error'); }
```

### Validation
- **Validate at boundaries**: API inputs, external data, user input
- **Fail fast**: Check preconditions early, throw immediately
- **Trust internal code**: Don't re-validate between internal modules

## Anti-Patterns (Never Do)

| Anti-Pattern | Why Bad | Instead |
|--------------|---------|---------|
| God objects/files | Unmaintainable, untestable | Split by responsibility |
| Deep nesting | Hard to read/debug | Early returns, extract functions |
| Magic numbers | Unclear intent | Named constants |
| Silent failures | Bugs hide | Throw or log errors |
| Copy-paste code | Maintenance nightmare | Extract shared function |
| Premature optimization | Wasted effort, complexity | Profile first, optimize proven bottlenecks |

## Testing Requirements

- **Business logic**: Must have unit tests
- **Edge cases**: Null, empty, boundary values tested
- **Error paths**: Test what happens when things fail
- **No implementation details**: Test behavior, not internals

## Comments

- **Why, not what**: Code shows what, comments explain why
- **Complex logic only**: Don't comment obvious code
- **Keep updated**: Outdated comments worse than none
- **TODOs**: Include ticket/issue reference

## Naming Conventions

```
Functions: verb + noun      → getUserById, calculateTotal, validateInput
Booleans: is/has/should    → isActive, hasPermission, shouldRetry
Constants: UPPER_SNAKE     → MAX_RETRIES, DEFAULT_TIMEOUT
Classes: PascalCase        → UserService, AuthMiddleware
Files: dot-separated       → user.service.ts, auth.middleware.ts
```

## Quality Checklist (Before Committing)

- [ ] Functions under 50 lines
- [ ] No deep nesting (max 4 levels)
- [ ] Error cases handled
- [ ] Input validation at boundaries
- [ ] No magic numbers
- [ ] Tests for business logic
- [ ] No copy-pasted code blocks
