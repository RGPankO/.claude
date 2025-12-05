# Architecture Guide

> **Context Loading**: This file is REFERENCED, not loaded. Main Claude should refer to this when making architectural decisions.

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

## Design Patterns

### Service Pattern
- Encapsulate business logic in service classes
- Services should be stateless when possible
- Use dependency injection for service dependencies
- Services handle one domain concept

### Repository Pattern
- Abstract data access behind interfaces
- Repositories handle data persistence logic
- Keep query logic separate from business logic
- Support different data sources transparently

### Factory Pattern
- Use factories for complex object creation
- Hide instantiation logic from consumers
- Support different implementations
- Centralize configuration-based creation

### Observer Pattern
- Implement event-driven architectures
- Decouple components through events
- Use for cross-cutting concerns
- Enable extensibility without modification

## Modularity Requirements

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

## Code Quality Standards

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

## Performance Considerations

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

## Refactoring Triggers

**Consider Refactoring When:**
- Code is duplicated in three or more places
- A function/class has multiple responsibilities
- Deep nesting makes code hard to follow
- Comments are needed to explain what code does
- Making a change requires modifications in multiple unrelated places
- Test setup is complex and fragile
- Performance bottlenecks are identified
- Security vulnerabilities are discovered