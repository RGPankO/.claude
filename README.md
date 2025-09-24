# Claude Code Starter Template

A clean, technology-agnostic starter template for Claude Code projects with architectural guidelines, specialized agents, and productivity-enhancing commands.

## 🎯 Purpose

This template provides a solid foundation for any software project, emphasizing:
- **Clean Architecture**: Modular, maintainable code structure
- **Context Preservation**: Specialized agents keep the main Claude context focused
- **Developer Productivity**: Smart commands automate repetitive tasks
- **Technology Agnostic**: Works with any programming language or framework

## 📁 Structure

```
.claude/
├── CLAUDE.md                 # Architectural guidelines and principles
├── agents/                   # Specialized AI agents
│   ├── codebase-analyzer.md      # Project structure analysis
│   ├── docs-explorer.md          # Documentation research
│   ├── playwright-qa-tester.md   # Browser-based testing
│   ├── senior-dev-consultant.md  # Expert technical guidance
│   ├── task-completion-validator.md # Task verification
│   └── test-generator.md         # Test suite creation
└── commands/                 # Custom slash commands
    ├── commit.md            # Smart git commits
    ├── deps.md              # Dependency management
    ├── fix.md               # Auto-fix code issues
    ├── status.md            # Project status overview
    └── test.md              # Smart test execution
```

## 🚀 Quick Start

1. **Clone this template**:
   ```bash
   git clone https://github.com/RGPankO/.claude.git my-project
   cd my-project
   rm -rf .git
   git init
   ```

2. **Customize for your project**:
   - Keep CLAUDE.md as your architectural guide
   - Agents work out of the box
   - Commands adapt to your project type automatically

3. **Start developing**:
   - Use `/status` to check project state
   - Use `/test` to run tests
   - Use `/fix` to auto-fix issues
   - Use `/deps` to manage dependencies
   - Use `/commit` for smart commits

## 🤖 Available Agents

### Core Development Agents

#### `senior-dev-consultant`
Expert guidance for complex technical decisions, architecture reviews, performance optimization, and security assessments. Uses advanced model for high-value consultations.

#### `codebase-analyzer`
Understands project structure without loading files into context. Identifies patterns, conventions, and architectural decisions. Essential for navigating large codebases.

#### `docs-explorer`
Researches documentation efficiently without cluttering context. Finds API references, configuration options, and best practices from project and external docs.

### Quality Assurance Agents

#### `playwright-qa-tester`
Comprehensive browser-based testing for web applications. Validates functionality, performance, accessibility, and cross-browser compatibility.

#### `task-completion-validator`
Verifies tasks are truly complete before marking done. Checks requirements, tests, documentation, and quality standards.

#### `test-generator`
Creates comprehensive test suites including unit, integration, and edge case tests. Follows project conventions and maximizes coverage.

## 📝 Available Commands

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