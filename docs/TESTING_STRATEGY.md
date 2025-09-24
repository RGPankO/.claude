# Testing Strategy Guide

> **Context Loading**: This file is REFERENCED, not loaded. Main Claude should refer to this when planning testing approaches.

## Testing Philosophy

Testing is not about achieving 100% coverage, but about building confidence in code behavior and preventing regressions. Focus on testing critical paths, edge cases, and complex logic.

## Test Categories

### Unit Tests
- Test individual functions/methods in isolation
- Mock external dependencies
- Fast execution (milliseconds)
- Test pure logic and calculations
- Cover edge cases and error conditions

### Integration Tests
- Test module interactions
- Use real dependencies where practical
- Test API endpoints with actual database
- Verify service layer integrations
- Test third-party service interactions

### End-to-End Tests
- Test complete user workflows
- Run in browser for web applications
- Test critical business paths
- Verify user-facing functionality
- Keep minimal but comprehensive

### Performance Tests
- Load testing for concurrent users
- Stress testing for system limits
- Memory leak detection
- Response time validation
- Resource usage monitoring

## Testing Principles

### Test Structure
- **Arrange**: Set up test data and conditions
- **Act**: Execute the function/action being tested
- **Assert**: Verify the expected outcome
- **Cleanup**: Reset state if necessary

### Test Quality
- Tests should be deterministic (same result every time)
- Tests should be independent (no shared state)
- Tests should be fast (seconds, not minutes)
- Tests should be readable (clear intent)
- Tests should fail for the right reasons

### What to Test
- **Critical Business Logic**: Core functionality that drives value
- **Edge Cases**: Boundary conditions, null values, empty collections
- **Error Paths**: Exception handling, validation failures
- **Integration Points**: API calls, database operations
- **Security Controls**: Authentication, authorization, input validation
- **Complex Algorithms**: Calculations, data transformations

### What NOT to Test
- Framework code (trust the framework)
- Simple getters/setters
- Configuration values
- Third-party libraries (unless wrapping them)
- UI styling (use visual regression tools instead)

## Test Data Management

### Fixtures
- Use minimal, focused test data
- Create factory functions for common objects
- Use builders for complex test objects
- Keep test data close to tests
- Avoid sharing mutable test data

### Database Testing
- Use transactions with rollback
- Create test-specific databases
- Seed with minimal required data
- Clean up after tests
- Consider in-memory databases for speed

## Mocking Strategies

### When to Mock
- External services (APIs, databases)
- File system operations
- Network calls
- Time-dependent operations
- Random number generation

### Mock Types
- **Stubs**: Return predefined values
- **Spies**: Record how they were called
- **Mocks**: Verify specific interactions
- **Fakes**: Simplified implementations

## Test Organization

### File Structure
```
tests/
├── unit/           # Fast, isolated tests
├── integration/    # Module interaction tests
├── e2e/           # End-to-end tests
├── fixtures/      # Test data and utilities
└── helpers/       # Test helper functions
```

### Naming Conventions
- Test files: `[module].test.js` or `[module].spec.js`
- Test suites: Describe the module/class being tested
- Test cases: Start with "should" or "when"
- Be descriptive: "should return user when valid ID provided"

## Coverage Guidelines

### Meaningful Coverage
- Aim for 70-80% overall coverage
- 90%+ for critical business logic
- 60-70% for UI components
- Focus on branch coverage over line coverage
- Don't test for the sake of numbers

### Coverage Metrics
- **Line Coverage**: Percentage of lines executed
- **Branch Coverage**: Percentage of decision paths taken
- **Function Coverage**: Percentage of functions called
- **Statement Coverage**: Percentage of statements executed

## Test Automation

### Continuous Integration
- Run tests on every commit
- Fail fast on test failures
- Generate coverage reports
- Run different test suites in parallel
- Cache dependencies for speed

### Test Environments
- **Local**: Fast unit tests during development
- **CI/CD**: Full test suite on commits
- **Staging**: E2E tests before production
- **Production**: Smoke tests after deployment

## Common Testing Patterns

### Parameterized Tests
Test multiple inputs with same logic:
```javascript
test.each([
  [1, 2, 3],
  [2, 3, 5],
  [3, 5, 8]
])('adds %i + %i to equal %i', (a, b, expected) => {
  expect(add(a, b)).toBe(expected);
});
```

### Async Testing
- Always return promises or use async/await
- Set appropriate timeouts
- Handle both success and failure cases
- Mock timers for time-dependent tests

### Error Testing
- Test error messages
- Verify error types
- Check error handling propagation
- Test recovery mechanisms

## Testing Anti-Patterns to Avoid

1. **Testing Implementation Details**: Test behavior, not how it's done
2. **Excessive Mocking**: Too many mocks make tests brittle
3. **Shared State**: Tests affecting each other
4. **Complex Setup**: If setup is complex, refactor the code
5. **Ignoring Failures**: Never skip or ignore failing tests
6. **Testing Everything**: Some code doesn't need tests
7. **Slow Tests**: Keep test suites fast
8. **Unclear Assertions**: Make failure messages helpful

## Framework-Specific Guidance

### JavaScript/TypeScript
- Jest, Vitest for unit/integration
- Playwright, Cypress for E2E
- Supertest for API testing
- React Testing Library for components

### Python
- pytest for all test types
- unittest for standard library
- Selenium for browser testing
- locust for load testing

### General Principles
- Use the testing framework native to your ecosystem
- Follow community best practices
- Leverage existing test utilities
- Keep test dependencies minimal