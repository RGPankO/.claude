# Claude Code Starter Template

A clean, technology-agnostic starter template for Claude Code projects with architectural guidelines, specialized agents, and productivity-enhancing commands.

## 🎯 Purpose

This template provides a solid foundation for any software project, emphasizing:
- **Clean Architecture**: Modular, maintainable code structure
- **Context Preservation**: Specialized agents keep the main Claude context focused
- **Developer Productivity**: Smart commands automate repetitive tasks
- **Technology Agnostic**: Works with any programming language or framework

## 📁 Structure

When cloned into your project's `.claude/` directory:

```
your-project/
├── CLAUDE.md                 # Move this to project root after cloning
└── .claude/                  # This entire repo gets cloned here
    ├── docs/                 # Detailed documentation guides
    │   ├── AGENT_TESTING_GUIDE.md
    │   ├── AGENT_WORKFLOWS.md
    │   ├── ARCHITECTURE_GUIDE.md
    │   ├── CODE_REVIEW_STANDARDS.md
    │   ├── COMMIT_GUIDELINES.md
    │   ├── CONTEXT_MANAGEMENT.md
    │   ├── DEBUGGING_APPROACH.md
    │   ├── FILE_ORGANIZATION.md
    │   ├── QA_PROTOCOLS.md
    │   └── TESTING_STRATEGY.md
    ├── agents/               # Specialized AI agents
    │   ├── codebase-analyzer.md
    │   ├── docs-explorer.md
    │   ├── docs-maintainer.md
    │   ├── investigator.md
    │   ├── playwright-qa-tester.md
    │   ├── senior-dev-consultant.md
    │   ├── senior-dev-implementer.md
    │   ├── strategic-planner.md
    │   ├── task-completion-validator.md
    │   └── test-generator.md
    └── commands/             # Custom slash commands
        ├── commit.md
        ├── debug.md
        ├── delegate.md
        ├── deps.md
        ├── docs-update.md
        ├── fix.md
        ├── plan.md
        ├── start_task.md
        ├── status.md
        └── test.md
```

## 🚀 Quick Start

1. **Clone into your project's .claude directory**:
   ```bash
   cd your-project
   git clone https://github.com/RGPankO/.claude.git .claude
   mv .claude/CLAUDE.md ./
   ```

2. **Customize for your project**:
   - Edit CLAUDE.md to add your project-specific context
   - Agents and commands work out of the box
   - Documentation guides are ready to reference

3. **Start developing**:
   - Use `/status` to check project state
   - Use `/test` to run tests
   - Use `/fix` to auto-fix issues
   - Use `/deps` to manage dependencies
   - Use `/commit` for smart commits

## 🤖 Available Agents

### Core Development Agents

#### `strategic-planner`
Creates comprehensive implementation plans for complex features using Opus (highest capability model). Generates living plan documents with phases, risks, dependencies, and actionable tasks.

#### `senior-dev-consultant`
Expert guidance for complex technical decisions, architecture reviews, performance optimization, and security assessments. Provides advice and recommendations, not implementation.

#### `senior-dev-implementer`
Writes production-quality code with all best practices built-in. Unlike the consultant who advises, this agent actually implements features with DRY/KISS principles, proper error handling, comprehensive testing, and clean architecture.

#### `codebase-analyzer`
Understands project structure without loading files into context. Identifies patterns, conventions, and architectural decisions. Essential for navigating large codebases.

#### `docs-explorer`
Researches documentation efficiently without cluttering context. Finds API references, configuration options, and best practices from project and external docs.

#### `investigator`
Deep research specialist that returns only essential findings. Tracks down root causes, researches APIs, analyzes performance issues, all while keeping the main context clean.

### `docs-maintainer`
Keeps documentation current by analyzing code changes and updating CLAUDE.md and docs/ files. Ensures documentation evolves with the codebase.

### Quality Assurance Agents

#### `playwright-qa-tester`
Comprehensive browser-based testing for web applications. Validates functionality, performance, accessibility, and cross-browser compatibility.

#### `task-completion-validator`
Verifies tasks are truly complete before marking done. Checks requirements, tests, documentation, and quality standards.

#### `test-generator`
Creates comprehensive test suites including unit, integration, and edge case tests. Follows project conventions and maximizes coverage.

## 📝 Available Commands

### `/plan [description]`
Creates comprehensive strategic plans for complex features using the highest-capability model (Opus). Generates living documents with phases, risks, and actionable tasks.

### `/delegate <task>`
Delegates complex tasks to specialized agents while keeping main Claude's context minimal. Acts as orchestrator, managing investigation and implementation through agents with detailed reporting standards.

### `/debug <issue>`
First-principles debugging for when normal approaches fail. Systematic methodology with stop-and-assess, dependency analysis, instrumentation strategy, hypothesis-driven testing, and user-assisted verification.

### `/start_task <description>`
Universal task management that sets up focused work session with context loading, todo creation, and clear next steps. Works across any project type.

### `/status [verbose|brief|focus:area]`
Comprehensive project overview including git status, running processes, tests, and todos.

### `/test [pattern|watch|coverage|failed]`
Smart test execution with pattern matching, watch mode, coverage reports, and failure retry.

### `/fix [all|lint|format|imports|types]`
Auto-fixes common issues: linting errors, formatting, import ordering, and simple type errors.

### `/deps [check|update|audit|clean|tree]`
Manages dependencies: checks for updates, security vulnerabilities, unused packages, and visualizes dependency tree.

### `/commit [instructions]`
Creates logical, well-structured commits with meaningful messages. Supports exclusions, custom messages, and smart grouping.

### `/docs-update [check|update|suggest]`
Analyzes code changes and updates documentation to keep CLAUDE.md and docs/ current. Helps maintain documentation as code evolves.

## 🏗️ Architectural Principles

The CLAUDE.md file contains comprehensive guidelines for:

- **Code Organization**: Modular design, separation of concerns, single responsibility
- **Design Patterns**: Service, Repository, Factory, Observer patterns
- **Quality Standards**: Maintainability, dependency management, error handling
- **Testing Strategy**: Unit, integration, E2E testing approaches
- **Performance**: Optimization guidelines and resource management
- **Documentation**: Code and architecture documentation standards

## 🔄 Workflow Patterns

### Context-Preserving Development
1. Use `codebase-analyzer` to understand structure
2. Use `docs-explorer` for documentation research
3. Implement with main Claude
4. Use `test-generator` for test creation
5. Use `playwright-qa-tester` for validation (web projects)
6. Use `task-completion-validator` for final check

### Smart Testing Workflow
```bash
/test           # Run all tests
/test watch     # Continuous testing
/test failed    # Retry failed tests
/test coverage  # Coverage report
```

### Dependency Management
```bash
/deps           # Overview of dependencies
/deps audit     # Security check
/deps update    # Safe updates
/deps clean     # Remove unused
```

## 💡 Best Practices

1. **Use agents proactively** to keep main context clean
2. **Run `/status` regularly** to stay aware of project state
3. **Use `/fix` before commits** to ensure code quality
4. **Run `/test` after changes** to catch issues early
5. **Check `/deps audit` periodically** for security
6. **Follow CLAUDE.md guidelines** for consistent architecture

## 🎯 Philosophy

This template embraces:
- **Complexity through simplicity**: Complex systems built from simple, focused modules
- **Context awareness**: Right tool for the right job
- **Automation over repetition**: Smart commands for common tasks
- **Quality from the start**: Guidelines and tools that prevent technical debt
- **Technology independence**: Principles over specific implementations

## 📚 Further Customization

### Adding Custom Agents
Create new `.md` files in `agents/` following the existing format:
```yaml
---
name: agent-name
description: When to use this agent
tools: Available tools
model: Model to use
---
```

### Adding Custom Commands
Create new `.md` files in `commands/` with:
```yaml
---
argument-hint: Expected arguments
description: Command purpose
---
```

## 🤝 Contributing

This template is designed to evolve. When you discover patterns that work well:
1. Update CLAUDE.md with new guidelines
2. Add new agents for specialized tasks
3. Create commands for repetitive workflows
4. Share your improvements

## 📄 License

This template is provided as-is for use in any project. Adapt and modify as needed for your specific requirements.

---

Built with ❤️ for developers who value clean, maintainable code and efficient workflows.