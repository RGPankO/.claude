# CLAUDE.md - Project Guidelines

This file provides guidance to Claude Code when working with code in this repository.

## Documentation Structure

**This file (CLAUDE.md)**: Always loaded - contains core principles. Keep it concise.

**Detailed Guides** (Referenced, not loaded - consult when needed):
- `.claude/docs/CODE_PRINCIPLES.md` - Code quality standards (hard limits, patterns)
- `.claude/docs/FILE_PRINCIPLES.md` - File organization (structure, naming)
- `.claude/docs/AGENT_GUIDE.md` - Base patterns for agent delegation
- `.claude/docs/ARCHITECTURE_GUIDE.md` - Project architecture and patterns
- `.claude/docs/KIRO_TASK_EXECUTION_GUIDE.md` - Development workflow for Kiro specs
- `.claude/docs/DELEGATE_GUIDE.md` - Agent delegation strategies
- `.claude/docs/DEBUG_GUIDE.md` - Systematic debugging methodologies
- `.claude/docs/POC_GUIDE.md` - Proof of concept patterns for validating unknowns

## Available Commands

- `/start` - Load essential project context (principles, architecture)
- `/spec` - Create comprehensive strategic plan for complex features, saved to .claude/specs/
- `/delegate` - Delegate tasks to agents, keeping context clean
- `/orchestrate` - Full workflow: analyze → implement → test → document → review
- `/debug` - First-principles debugging for complex issues
- `/poc` - Proof of concept to validate technical feasibility before full build
- `/commit` - Smart git commits with logical grouping
- `/docs-update` - Analyze and update documentation
- `/test` - Create QA test request documents
- `/kiro` - Execute tasks from Kiro implementation plans
- `/kiro-create` - Create a new Kiro spec (requirements.md, design.md, tasks.md)
- `/kiro-review` - Review completed Kiro tasks against specifications

## Core Principles

### Ask Before Assuming

- **When instructions are ambiguous**: Ask for clarification rather than assuming
- **When multiple interpretations possible**: Request specific details
- **When requirements seem incomplete**: Ask about missing details
- **Better to ask than sorry**: Clarify first, implement second

### Code Quality (See CODE_PRINCIPLES.md for details)

| Rule | Limit |
|------|-------|
| Function length | Max 50 lines |
| Parameters | Max 4 (use object for more) |
| Nesting depth | Max 4 levels |
| File length | Max 300 lines |

### File Organization (See FILE_PRINCIPLES.md for details)

- One responsibility per file
- Group by feature, not type
- Consistent naming conventions
- Temp files in designated directory only

## Available Expert Agents

See `.claude/docs/DELEGATE_GUIDE.md` for detailed usage patterns.

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| `strategic-planner` | Implementation planning | Start of complex features |
| `senior-dev-consultant` | Expert advice | Architecture decisions, complex debugging |
| `senior-dev-implementer` | Production code | Complex features needing senior-level quality |
| `task-completion-validator` | Verify completeness | Before marking tasks done |
| `investigator` | Deep research | Bug investigation, API research |
| `codebase-analyzer` | Understand structure | Before adding new modules |
| `docs-explorer` | Documentation research | Library/API documentation lookup |
| `test-generator` | Create test suites | After implementing features |
| `docs-maintainer` | Update documentation | After significant changes |

**Quick Reference**:
- Use `codebase-analyzer` instead of opening many files
- Use `docs-explorer` instead of loading extensive docs
- Run multiple agents in parallel when tasks are independent

## Documentation Maintenance

After implementing significant changes:
- Pattern used multiple times → Update architecture guide
- Bug fixed with learnings → Document in debugging guide
- New conventions emerge → Update relevant guides

Run `/docs-update check` to analyze if documentation needs updating.

## Continuous Improvement

This document should evolve with the project. Update these guidelines when you discover new patterns, anti-patterns, or better approaches.
