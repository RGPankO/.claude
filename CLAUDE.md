# CLAUDE.md - Architectural Guidelines

This file provides guidance to Claude Code when working with code in this repository to maintain clean, modular, and scalable architecture.

## Core Architectural Principles

### Code Organization Philosophy

**Modular Design**
- Keep files focused on a single, well-defined purpose
- If scrolling multiple times is needed to understand a module, consider splitting it
- Functions should generally fit within a single screen view when reasonable
- Classes should have clear, cohesive responsibilities

**Complexity Guidelines**
- Monitor cyclomatic and cognitive complexity, not just line counts
- Prioritize readability and maintainability over arbitrary size limits
- Split code when it naturally separates into distinct concerns
- Consider the "rule of three": extract common patterns after three occurrences

### Separation of Concerns

**Layered Architecture**
- **Presentation Layer**: User interface and user interaction handling
- **Business Logic Layer**: Core domain logic and business rules
- **Data Access Layer**: Data persistence and retrieval
- **Integration Layer**: External system communications
- **Cross-cutting Concerns**: Logging, security, configuration

**Module Organization Patterns**
```
src/
├── core/               # Core business logic and domain models
├── interfaces/         # External interfaces (UI, API, CLI)
├── infrastructure/     # Technical implementations
├── shared/            # Shared utilities and common code
└── config/            # Configuration management
```

### Single Responsibility Principle

**Functions and Methods**
- Each function should do ONE thing and do it well
- Function names should clearly express their single purpose
- If a function name contains "and" or multiple verbs, consider splitting
- Pure functions (no side effects) are preferred where possible

**Classes and Modules**
- Each class should have ONE reason to change
- Modules should export related functionality
- Avoid "god classes" that know too much or do too much
- Prefer composition over inheritance

## Development Principles

### Code Quality Standards

**Maintainability**
- Write self-documenting code with clear naming
- Keep functions small and focused
- Avoid deep nesting (max 3-4 levels)
- Extract complex conditionals into well-named functions
- Use consistent patterns throughout the codebase

**Dependency Management**
- Depend on abstractions, not concretions
- Higher-level modules shouldn't depend on lower-level modules
- Both should depend on abstractions
- No circular dependencies allowed
- Minimize coupling between modules

**Error Handling**
- Fail fast with clear error messages
- Handle errors at the appropriate level
- Use structured error types/classes
- Log errors with sufficient context
- Never silently swallow exceptions

### Design Patterns

**Service Pattern**
- Encapsulate business logic in service classes
- Services should be stateless when possible
- Use dependency injection for service dependencies
- Services handle one domain concept

**Repository Pattern**
- Abstract data access behind interfaces
- Repositories handle data persistence logic
- Keep query logic separate from business logic
- Support different data sources transparently

**Factory Pattern**
- Use factories for complex object creation
- Hide instantiation logic from consumers
- Support different implementations
- Centralize configuration-based creation

**Observer Pattern**
- Implement event-driven architectures
- Decouple components through events
- Use for cross-cutting concerns
- Enable extensibility without modification

## Architecture Guidelines

### Modularity Requirements

**Module Characteristics**
- High cohesion within modules
- Low coupling between modules
- Clear, well-defined interfaces
- Independent testability
- Single, focused purpose

**Interface Design**
- Design interfaces from the consumer's perspective
- Keep interfaces small and focused
- Use interface segregation principle
- Document interface contracts clearly
- Version interfaces when breaking changes occur

### Testing Strategy

**Test Organization**
- Unit tests for individual functions/methods
- Integration tests for module interactions
- End-to-end tests for critical user paths
- Performance tests for critical operations
- Each module should be independently testable

**Test Principles**
- Tests should be fast, isolated, and repeatable
- Test behavior, not implementation
- Use test doubles (mocks, stubs) appropriately
- Maintain test code quality like production code
- Aim for meaningful coverage, not 100%

### Performance Considerations

**Optimization Guidelines**
- Profile before optimizing
- Optimize algorithms before micro-optimizations
- Consider time and space complexity
- Cache expensive computations appropriately
- Use lazy loading and pagination for large datasets

**Resource Management**
- Clean up resources properly (connections, handles, subscriptions)
- Implement proper connection pooling
- Monitor memory usage and leaks
- Use streaming for large data processing
- Implement appropriate timeouts

## Development Workflow

### Code Review Checklist

**Functionality**
- [ ] Does the code accomplish the intended goal?
- [ ] Are all requirements addressed?
- [ ] Are edge cases properly handled?
- [ ] Is error handling comprehensive?

**Design**
- [ ] Is the code well-organized and modular?
- [ ] Are responsibilities properly separated?
- [ ] Are dependencies properly managed?
- [ ] Is the solution appropriately complex?

**Maintainability**
- [ ] Is the code readable and self-documenting?
- [ ] Are naming conventions consistent?
- [ ] Is there appropriate documentation?
- [ ] Will future developers understand this code?

**Performance**
- [ ] Are there any obvious bottlenecks?
- [ ] Is resource usage appropriate?
- [ ] Are database queries optimized?
- [ ] Is caching used effectively?

**Security**
- [ ] Is input validation comprehensive?
- [ ] Are secrets properly managed?
- [ ] Is authentication/authorization correct?
- [ ] Are there any injection vulnerabilities?

### Refactoring Triggers

**Consider Refactoring When**
- Code is duplicated in three or more places
- A function/class has multiple responsibilities
- Deep nesting makes code hard to follow
- Comments are needed to explain what code does
- Making a change requires modifications in multiple unrelated places
- Test setup is complex and fragile
- Performance bottlenecks are identified
- Security vulnerabilities are discovered

### Documentation Standards

**Code Documentation**
- Document WHY, not WHAT (code shows what)
- Document complex algorithms and business rules
- Document external dependencies and integrations
- Keep documentation close to code
- Update documentation with code changes

**Architecture Documentation**
- Document high-level system design
- Explain key architectural decisions
- Describe module interactions
- Document deployment architecture
- Maintain API documentation

## Quality Assurance

### Pre-Development Checklist
- [ ] Requirements are clear and complete
- [ ] Design supports the requirements
- [ ] Architecture can accommodate the feature
- [ ] Dependencies are identified and available
- [ ] Testing approach is defined

### Development Checklist
- [ ] Code follows established patterns
- [ ] Tests are written alongside code
- [ ] Error cases are handled
- [ ] Performance impact is considered
- [ ] Security implications are addressed

### Pre-Deployment Checklist
- [ ] All tests pass
- [ ] Code has been reviewed
- [ ] Documentation is updated
- [ ] Performance is acceptable
- [ ] Security scan is clean
- [ ] Rollback plan exists

## Warning Signs - Technical Debt Indicators

**Architecture Smells**
- Shotgun surgery (one change requires many file modifications)
- Feature envy (modules excessively interested in other modules)
- Inappropriate intimacy (modules know too much about each other)
- Large classes/modules doing too much
- Divergent change (module changes for multiple reasons)

**Code Smells**
- Long parameter lists
- Duplicate code blocks
- Dead code
- Speculative generality
- Temporary fields
- Message chains
- Middle man classes

**Process Smells**
- Increasing bug rates
- Longer development cycles
- Difficult deployments
- Frequent hotfixes
- Developer frustration

## Available Expert Agents

### Senior Developer Consultant Agent (`senior-dev-consultant`)

**Use this agent for expert second opinions on**:
- **Complex architectural decisions** requiring deep technical evaluation
- **Difficult debugging scenarios** where initial attempts haven't resolved issues
- **Performance optimization** requiring advanced analysis and solutions
- **Security-sensitive code reviews** for authentication, data handling, or API security
- **Database schema design** or complex migration planning
- **API design decisions** with long-term architectural implications
- **Refactoring strategies** for large-scale code reorganization
- **Code quality reviews** before major releases or deployments
- **Algorithm optimization** for complex calculations
- **Concurrency issues** and race condition debugging
- **Memory leaks** and resource optimization problems

**When to invoke**: Proactively use when facing tasks that would benefit from senior-level expertise. Examples:
- "Let me consult the senior developer agent for guidance on this architecture decision"
- "This debugging issue is complex - I'll get a second opinion from the senior consultant"
- "Before implementing this critical feature, I'll verify approach with the senior dev agent"

### Playwright QA Tester Agent (`playwright-qa-tester`)

**Use this agent for comprehensive browser-based testing**:
- **New feature validation** after implementing UI components or user workflows
- **Bug reproduction and investigation** for reported issues with visual or interaction problems
- **Cross-browser compatibility testing** before deployments
- **Performance testing** for page load times and rendering metrics
- **Accessibility validation** for WCAG compliance and keyboard navigation
- **Regression testing** to ensure existing functionality remains intact
- **User workflow validation** for critical business paths
- **Console error monitoring** to catch JavaScript exceptions
- **Visual regression testing** for layout and styling consistency
- **Form validation testing** including edge cases and error states

**When to invoke**: Use after implementing features or when quality assurance is needed. Examples:
- "I've implemented the new checkout flow - let me use the QA tester agent to validate it"
- "Users report visual glitches - I'll have the playwright agent investigate"
- "Before deployment, let me run comprehensive browser tests with the QA agent"

### Task Completion Validator Agent (`task-completion-validator`)

**Use this agent to verify development tasks are truly complete**:
- **Feature implementation validation** to ensure all requirements are met
- **Bug fix verification** confirming the issue is resolved without regressions
- **Database migration audits** for proper structure and rollback capability
- **API endpoint validation** including error handling and edge cases
- **Security implementation checks** for auth, authorization, and data protection
- **Test coverage assessment** ensuring adequate unit and integration tests
- **Documentation completeness** verifying updated docs and comments
- **Code quality validation** checking standards and best practices
- **Performance requirement verification** for response times and resource usage
- **Deployment readiness assessment** before marking tasks as done

**When to invoke**: Use before marking any significant task as complete. Examples:
- "I've finished the authentication system - let me validate with the completion validator"
- "Bug fix is done - I'll verify it's truly complete with the validator agent"
- "Database migration is ready - let me run the task validator to ensure it's production-ready"

### Codebase Analyzer Agent (`codebase-analyzer`)

**Use this agent to understand project structure and patterns**:
- **Architecture exploration** to understand project organization and design patterns
- **Pattern identification** finding conventions and coding standards used throughout
- **Implementation location** discovering where features and modules are implemented
- **Dependency mapping** understanding relationships between modules
- **Convention discovery** identifying naming, structure, and style patterns
- **Refactoring planning** finding code duplication and improvement opportunities
- **Integration points** understanding how modules connect and communicate
- **Framework usage** discovering how frameworks and libraries are utilized
- **Project navigation** efficiently finding relevant code without loading everything
- **Technical debt assessment** identifying areas needing improvement

**When to invoke**: Use when you need to understand the codebase without loading extensive files. Examples:
- "I need to add a new payment module - let me analyze the codebase structure first"
- "Where is authentication handled? I'll use the codebase analyzer to find out"
- "Let me understand the service patterns used in this project with the analyzer"

### Documentation Explorer Agent (`docs-explorer`)

**Use this agent for comprehensive documentation research**:
- **API documentation** understanding endpoints, parameters, and responses
- **Library research** exploring third-party library features and usage
- **Configuration discovery** finding environment variables and settings
- **Project conventions** understanding coding standards and guidelines
- **Setup instructions** finding installation and deployment procedures
- **Troubleshooting guides** researching known issues and solutions
- **Example mining** finding code samples and usage patterns
- **Best practices** discovering recommended approaches and patterns
- **Migration guides** understanding upgrade paths and breaking changes
- **Integration documentation** learning how to connect with external services

**When to invoke**: Use when you need documentation insights without loading all docs. Examples:
- "How do I use the Redis caching features? Let me explore the documentation"
- "What environment variables are available? I'll check with the docs explorer"
- "Let me research the API guidelines with the documentation explorer agent"

### Test Generator Agent (`test-generator`)

**Use this agent to create comprehensive test suites**:
- **Unit test generation** creating tests for functions and methods
- **Integration test creation** testing module interactions and APIs
- **Edge case testing** generating tests for boundary conditions
- **Error scenario testing** creating tests for failure cases
- **Test fixture generation** creating mock data and test utilities
- **Coverage improvement** identifying and filling test gaps
- **Test refactoring** updating tests after code changes
- **Performance test creation** generating load and stress tests
- **Regression test suites** preventing bug reintroduction
- **Test documentation** creating clear, maintainable test descriptions

**When to invoke**: Use when you need to create or improve test coverage. Examples:
- "I've implemented the auth module - let me generate comprehensive tests for it"
- "Our coverage is low on the payment module - I'll use the test generator"
- "After refactoring, I need to update the tests with the test generator agent"

### Agent Collaboration Patterns

**Context-Preserving Workflow**
1. Use `codebase-analyzer` to understand project structure without loading files
2. Use `docs-explorer` to research documentation without cluttering context
3. Implement feature with main Claude using insights from agents
4. Use `test-generator` to create tests in isolation
5. Use `playwright-qa-tester` for browser validation (web projects)
6. Use `task-completion-validator` for final verification

**Sequential Validation**
1. Implement feature with main Claude
2. Use `playwright-qa-tester` for functional validation
3. Use `task-completion-validator` for completeness check
4. Mark task as done only after validation passes

**Parallel Exploration**
- Run `codebase-analyzer` and `docs-explorer` simultaneously for project understanding
- Use `senior-dev-consultant` for architecture review while implementing
- Generate tests with `test-generator` while main Claude continues development
- Can run multiple agents for different aspects simultaneously

**Context Management Best Practices**
- Use `codebase-analyzer` instead of opening many files to understand structure
- Use `docs-explorer` instead of loading extensive documentation
- Use `test-generator` to avoid cluttering main context with test code
- Delegate specialized tasks to agents to keep main Claude focused

**Cost Optimization Notes**:
- The `senior-dev-consultant` uses a more capable model (higher cost) - use for complex problems
- The `codebase-analyzer` and `docs-explorer` use efficient models (lower cost) - use liberally
- The `test-generator` uses a balanced model - use when comprehensive tests are needed

## Continuous Improvement

This document should evolve with the project. When you discover new patterns, anti-patterns, or better approaches, update these guidelines. The goal is to maintain a sustainable, high-quality codebase that remains maintainable as it grows.

Remember: Good architecture enables change. These guidelines ensure the codebase can adapt to new requirements while maintaining quality and developer productivity.