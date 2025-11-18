# Code Review Standards & Quality Guidelines

This document defines the code quality standards and review criteria for maintaining a clean, scalable, and performant codebase.

## Core Development Principles

### DRY (Don't Repeat Yourself)
- Extract common patterns after 3+ occurrences
- Create reusable modules, services, and utilities
- Centralize configuration and constants
- Use composition for shared behaviors
- Never duplicate business logic or complex calculations

### KISS (Keep It Simple, Stupid)
- Prefer simple, readable solutions over clever optimizations
- Avoid premature optimization except for proven bottlenecks
- Write code for humans first, performance second
- If it needs extensive comments to explain, refactor it
- Clear intent over complex abstractions

### Modular Architecture
- Everything should be handled by a dedicated module/service
- Modules should be self-contained with clear interfaces
- Use dependency injection for module relationships
- Keep modules focused on a single responsibility
- Each domain concern should have one authoritative module

### Separation of Concerns
- Presentation layer separate from business logic
- Data access separate from business rules
- External integrations isolated from core logic
- Configuration separate from implementation
- Clear boundaries between layers

## Code Quality Standards

### File Organization
- **One module/class per file** - no monolithic files
- **Maximum file length**: ~200-300 lines for services/controllers, ~150 lines for utilities
- **Clear file naming**: Follow project conventions (camelCase, kebab-case, PascalCase)
- **Logical folder structure**: Organize by feature or layer (services/, controllers/, utils/, models/)

### Function Guidelines
- **Single Responsibility**: Each function does ONE thing well
- **Maximum function length**: Should fit on a single screen (~40-50 lines)
- **Parameter count**: Max 3-4 parameters, use config objects for more
- **Nesting depth**: Maximum 3-4 levels of nesting
- **Early returns**: Use guard clauses to reduce nesting
- **Pure functions preferred**: Minimize side effects in calculations

### Code Clarity
- **Self-documenting names**: Functions/variables should clearly express intent
- **No magic numbers**: Use named constants (MAX_RETRIES, DEFAULT_TIMEOUT)
- **Avoid abbreviations**: `userRepository` not `usrRepo`
- **Consistent naming**: `getUserById` not `fetchUser` + `getAccountById`
- **Domain precision**: Use appropriate data types for domain (BigInt for precision, Decimal for money)

## Domain-Specific Considerations

### State Management
- **Immutable updates**: Never mutate state directly
- **State validation**: Validate all state transitions
- **Predictable states**: Clear state machine with defined transitions
- **Serializable**: State should be serializable when needed
- **Event-driven**: State changes should emit events for observability

### Data Integrity
- **Precision matters**: Use appropriate numeric types for domain (BigInt, Decimal)
- **Validation everywhere**: Validate at boundaries (API, database, external services)
- **Constraint enforcement**: Enforce business rules at data layer
- **Audit trails**: Track critical state changes
- **Idempotency**: Operations should be safely repeatable

### API Design
- **Consistent patterns**: Follow REST/GraphQL conventions
- **Input validation**: Validate and sanitize all inputs
- **Error responses**: Provide meaningful, actionable errors
- **Versioning**: Plan for API evolution
- **Rate limiting**: Protect against abuse

### Asynchronous Operations
- **Error handling**: Handle promise rejections
- **Timeouts**: Set reasonable timeouts for external calls
- **Retry logic**: Implement exponential backoff where appropriate
- **Cancellation**: Support operation cancellation
- **Resource cleanup**: Always clean up (connections, subscriptions, timers)

## Code Review Checklist

### ✅ Functionality
- [ ] Does the code accomplish the intended feature?
- [ ] Are all requirements met?
- [ ] Are edge cases handled (null values, empty arrays, boundary conditions)?
- [ ] Is error handling comprehensive?
- [ ] Are state transitions properly managed?
- [ ] Are business rules correctly implemented?

### ✅ Code Quality
- [ ] Is the code DRY (no unnecessary duplication)?
- [ ] Does it follow KISS principle?
- [ ] Are modules properly separated?
- [ ] Is the code self-documenting?
- [ ] Are functions focused on single responsibility?
- [ ] Is nesting depth reasonable?
- [ ] Are naming conventions consistent?

### ✅ Performance
- [ ] Are there any obvious performance bottlenecks?
- [ ] Are database queries optimized (N+1 avoided)?
- [ ] Are API calls batched when possible?
- [ ] Is memory usage reasonable?
- [ ] Are expensive operations cached appropriately?
- [ ] Are blocking operations minimized?

### ✅ Testing
- [ ] Are there unit tests for business logic?
- [ ] Are edge cases tested?
- [ ] Are error scenarios tested?
- [ ] Are integration tests present for critical paths?
- [ ] Is test coverage adequate?

### ✅ Security
- [ ] Is input validation comprehensive?
- [ ] Are authentication/authorization checks correct?
- [ ] Are there any injection vulnerabilities (SQL, XSS, command)?
- [ ] Are secrets properly managed (not hardcoded)?
- [ ] Is sensitive data encrypted?
- [ ] Are rate limits implemented where needed?

### ✅ Documentation
- [ ] Is complex business logic documented?
- [ ] Are API contracts clear?
- [ ] Are assumptions documented?
- [ ] Is the data model explained?
- [ ] Are module dependencies documented?

## Warning Signs - Code Smells

### 🚨 Architecture Smells
- **God objects**: Classes/modules that know or do too much
- **State mutations**: Direct modification of shared state
- **Tight coupling**: Modules directly accessing others' internals
- **Missing validation**: No input/boundary checking
- **Shotgun surgery**: One change requires modifications in many places

### 🚨 Performance Smells
- **N+1 queries**: Database queries in loops
- **Missing indexes**: Slow database queries
- **Blocking I/O**: Synchronous operations in request handlers
- **Memory leaks**: Not cleaning up listeners, subscriptions, or connections
- **No caching**: Repeatedly computing/fetching same data

### 🚨 Security Smells
- **Client trust**: Trusting client input without validation
- **Missing auth**: Endpoints without authentication checks
- **Hardcoded secrets**: API keys or passwords in code
- **No rate limiting**: APIs vulnerable to abuse
- **Insufficient logging**: Security events not tracked

### 🚨 Data Smells
- **Missing transactions**: Related operations not atomic
- **Stale data**: No cache invalidation strategy
- **Data duplication**: Same data stored in multiple places
- **Lack of constraints**: No database-level validation
- **Manual ID generation**: Not using database sequences/UUIDs

## Refactoring Triggers

**Immediate Refactoring Required When:**
- Security vulnerabilities discovered
- Performance degrades significantly
- Memory leaks detected
- Data integrity issues occur
- Function exceeds 80 lines
- Module handles multiple unrelated concerns
- Code duplicated in 3+ places

**Consider Refactoring When:**
- Function approaches 50 lines
- Module dependencies become complex
- Test setup becomes complicated
- API response times increase
- Database queries become slow

## Best Practices

### Error Handling
```javascript
// ✅ GOOD: Comprehensive error handling
async function fetchUserData(userId) {
  try {
    const response = await api.get(`/users/${userId}`);

    if (!response.ok) {
      throw new Error(`Failed to fetch user: ${response.statusText}`);
    }

    return response.json();
  } catch (error) {
    logger.error('User fetch failed', { userId, error: error.message });
    throw new AppError('Unable to load user data', { cause: error });
  }
}

// ❌ BAD: Silent failures
async function fetchUserData(userId) {
  try {
    return await api.get(`/users/${userId}`);
  } catch (e) {
    return null; // Swallows error, caller doesn't know what happened
  }
}
```

### Input Validation
```javascript
// ✅ GOOD: Validate at boundaries
function createUser(userData) {
  // Validate inputs
  if (!userData.email || !isValidEmail(userData.email)) {
    throw new ValidationError('Invalid email address');
  }

  if (!userData.name || userData.name.length < 2) {
    throw new ValidationError('Name must be at least 2 characters');
  }

  // Sanitize inputs
  const sanitizedData = {
    email: userData.email.toLowerCase().trim(),
    name: sanitize(userData.name),
  };

  return userRepository.create(sanitizedData);
}

// ❌ BAD: No validation
function createUser(userData) {
  return userRepository.create(userData); // Trusts input blindly
}
```

### State Management
```javascript
// ✅ GOOD: Immutable state updates
function updateUserProfile(state, updates) {
  return {
    ...state,
    user: {
      ...state.user,
      ...updates,
      updatedAt: new Date()
    }
  };
}

// ❌ BAD: Direct mutation
function updateUserProfile(state, updates) {
  state.user.name = updates.name; // Direct mutation
}
```

### Dependency Injection
```javascript
// ✅ GOOD: Dependency injection
class UserService {
  constructor(userRepository, emailService) {
    this.userRepository = userRepository;
    this.emailService = emailService;
  }

  async createUser(userData) {
    const user = await this.userRepository.create(userData);
    await this.emailService.sendWelcome(user.email);
    return user;
  }
}

// ❌ BAD: Direct instantiation
class UserService {
  constructor() {
    this.userRepository = new UserRepository(); // Creates tight coupling
    this.emailService = new EmailService();
  }
}
```

### Database Transactions
```javascript
// ✅ GOOD: Atomic operations
async function transferFunds(fromId, toId, amount) {
  return await db.transaction(async (tx) => {
    await tx.accounts.update({
      where: { id: fromId },
      data: { balance: { decrement: amount } }
    });

    await tx.accounts.update({
      where: { id: toId },
      data: { balance: { increment: amount } }
    });

    await tx.auditLog.create({
      data: { action: 'transfer', fromId, toId, amount }
    });
  });
}

// ❌ BAD: No transaction
async function transferFunds(fromId, toId, amount) {
  await updateBalance(fromId, -amount); // Could fail after this
  await updateBalance(toId, amount);    // Leaving inconsistent state
}
```

## Review Process

1. **Self-Review First**: Test your changes locally
2. **Run Tests**: Ensure all tests pass (unit, integration, E2E)
3. **Security Check**: Verify input validation and authorization
4. **Performance Check**: Ensure no performance regressions
5. **Documentation**: Update relevant docs and code comments

## Enforcement

- **Automated Linting**: ESLint, Prettier, or language-specific linters
- **Unit Tests**: Business logic test coverage
- **Integration Tests**: API and database interaction tests
- **E2E Tests**: Playwright or Cypress for critical user flows
- **CI/CD**: Automated checks on every commit
- **Manual Review**: Code review by peers

Remember: Code quality directly impacts maintainability, security, and user experience. Write code that future developers (including yourself) will thank you for.