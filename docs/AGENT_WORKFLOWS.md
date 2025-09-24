# Agent Workflows Guide

> **Context Loading**: This file is REFERENCED, not loaded. Main Claude should consult this to understand optimal agent usage patterns.

## Agent Philosophy

Agents are specialized tools that handle specific domains of work, allowing the main Claude to maintain a clean, focused context. Think of agents as expert consultants you bring in for specific tasks.

## When to Use Agents vs Direct Action

### Use Agents When:
- Task requires exploring many files
- Researching documentation extensively
- Generating large amounts of code (tests)
- Analyzing without modifying
- Need specialized expertise
- Want to preserve main context

### Handle Directly When:
- Making small, focused changes
- Already have necessary context
- Task is straightforward
- Need immediate execution
- Working on critical path

## Agent Capabilities Matrix

| Agent | Best For | Model | Cost | When to Use |
|-------|----------|-------|------|-------------|
| `senior-dev-consultant` | Complex decisions | Advanced | High | Architecture, debugging, security |
| `codebase-analyzer` | Structure understanding | Efficient | Low | Project exploration, patterns |
| `docs-explorer` | Documentation research | Efficient | Low | Learning APIs, finding examples |
| `test-generator` | Test creation | Balanced | Medium | Coverage improvement, TDD |
| `playwright-qa-tester` | Browser testing | Balanced | Medium | UI validation, E2E tests |
| `task-completion-validator` | Verification | Balanced | Medium | Before marking done |
| `docs-maintainer` | Documentation updates | Balanced | Medium | Keeping docs current |

## Workflow Patterns

### 1. Exploration Before Implementation
```
1. Use codebase-analyzer to understand structure
2. Use docs-explorer for API/library research
3. Implement with main Claude
4. Use test-generator for tests
5. Use task-completion-validator to verify
```

**Benefits**: Clean context, comprehensive understanding, faster implementation

### 2. Test-Driven Development
```
1. Use test-generator to create test specs
2. Implement with main Claude to pass tests
3. Use playwright-qa-tester for UI validation
4. Use task-completion-validator for final check
```

**Benefits**: Clear requirements, regression prevention, quality assurance

### 3. Debugging Complex Issues
```
1. Use codebase-analyzer to trace data flow
2. Use senior-dev-consultant for strategy
3. Debug with main Claude
4. Use test-generator to prevent regression
```

**Benefits**: Expert guidance, systematic approach, permanent fix

### 4. Documentation-Driven Learning
```
1. Use docs-explorer for comprehensive research
2. Use codebase-analyzer for implementation examples
3. Implement with main Claude
4. Use docs-maintainer to update project docs
```

**Benefits**: Thorough understanding, best practices, knowledge preservation

## Parallel Agent Execution

### When to Run Agents in Parallel
- Independent research tasks
- Multiple aspects of same problem
- Time-critical investigations
- Comprehensive analysis needed

### Example Parallel Workflows
```
// Parallel Research
- codebase-analyzer: Find all auth implementations
- docs-explorer: Research auth best practices
- senior-dev-consultant: Review security approach

// Parallel Validation
- playwright-qa-tester: Test UI flows
- test-generator: Create unit tests
- task-completion-validator: Check requirements
```

## Agent Communication Patterns

### Sequential Handoff
Agent A completes → Results to main Claude → Main Claude invokes Agent B

**Use when**: Results from one agent inform the next

### Parallel Gathering
Multiple agents → All results to main Claude → Main Claude synthesizes

**Use when**: Need multiple perspectives simultaneously

### Verification Loop
Main Claude implements → Agent validates → Main Claude fixes → Agent re-validates

**Use when**: Quality assurance is critical

## Optimizing Agent Usage

### Cost Optimization
1. Use efficient agents (analyzer, explorer) liberally
2. Reserve senior-dev-consultant for complex problems
3. Batch similar requests together
4. Cache agent results when possible

### Time Optimization
1. Run independent agents in parallel
2. Use focused, specific requests
3. Provide clear context to agents
4. Skip agents for trivial tasks

### Context Optimization
1. Never load what agents can explore
2. Summarize agent results
3. Keep only essential info in main context
4. Use agents for large file operations

## Agent Request Best Practices

### Clear Instructions
```
❌ Bad: "Check the authentication"
✅ Good: "Analyze the JWT authentication implementation in the auth module, focusing on token refresh logic and security vulnerabilities"
```

### Specific Scope
```
❌ Bad: "Look at the tests"
✅ Good: "Review unit test coverage for the user service, identify missing edge cases for user registration"
```

### Expected Output
```
❌ Bad: "Find issues"
✅ Good: "List top 5 performance bottlenecks with specific file locations and suggested fixes"
```

## Common Agent Combinations

### New Feature Implementation
1. `codebase-analyzer` - Understand existing patterns
2. `docs-explorer` - Research required APIs
3. Main Claude - Implement feature
4. `test-generator` - Create comprehensive tests
5. `playwright-qa-tester` - Validate UI
6. `task-completion-validator` - Final verification

### Bug Investigation
1. `codebase-analyzer` - Trace issue source
2. `senior-dev-consultant` - Debugging strategy
3. Main Claude - Fix implementation
4. `test-generator` - Regression tests

### Refactoring
1. `codebase-analyzer` - Map dependencies
2. `senior-dev-consultant` - Refactoring plan
3. Main Claude - Execute refactoring
4. `test-generator` - Update tests
5. `task-completion-validator` - Verify nothing broken

### Documentation Update
1. `codebase-analyzer` - Current implementation
2. `docs-explorer` - Existing documentation
3. `docs-maintainer` - Update documentation
4. Main Claude - Review and integrate

## Agent Limitations

### What Agents Cannot Do
- Make architectural decisions (they advise)
- Fix bugs directly (they identify)
- Deploy code (they validate)
- Make business decisions (they inform)

### When NOT to Use Agents
- Emergency production fixes
- Simple, clear tasks
- When you have full context
- Real-time user interactions
- Tasks requiring immediate action

## Measuring Agent Effectiveness

### Success Indicators
- Fewer bugs reaching production
- Faster feature implementation
- Better test coverage
- More maintainable code
- Less context switching

### Anti-Patterns to Avoid
1. **Agent Overuse**: Using agents for trivial tasks
2. **Context Pollution**: Loading agent results unnecessarily
3. **Sequential Bottleneck**: Not using parallel execution
4. **Vague Requests**: Unclear agent instructions
5. **Ignoring Results**: Not acting on agent findings

## Future Agent Considerations

### Potential New Agents
- Performance Profiler Agent
- Security Auditor Agent
- Dependency Manager Agent
- Migration Assistant Agent
- API Designer Agent

### Evolution Guidelines
- Agents should remain focused
- Overlap should be minimal
- Each should excel in its domain
- Cost/benefit should be clear
- Integration should be seamless