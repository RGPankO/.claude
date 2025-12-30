# Claude Code Skeleton

A portable, optimized skeleton for Claude Code projects with enforceable principles, specialized agents, and workflow commands.

## Purpose

This skeleton provides:
- **Enforceable Standards**: CODE_PRINCIPLES and FILE_PRINCIPLES with hard limits
- **Specialized Agents**: 9 agents for delegation, keeping main context clean
- **Workflow Commands**: 12 slash commands for common development workflows
- **Kiro IDE Integration**: Generate and work with [Kiro](https://kiro.dev) specs from Amazon
- **Antigravity Testing**: Create human-readable QA test requests for [Google Antigravity](https://antigravity.google/)
- **~90% Portable**: Only ARCHITECTURE_GUIDE needs project customization

## Quick Start

Clone directly into your project root:

```bash
cd your-project
git clone https://github.com/RGPankO/.claude.git .
rm -rf .git   # Remove skeleton's git history
git init      # Start fresh git history (or skip if existing repo)
```

Everything is already in place - no moving files needed:
- `CLAUDE.md` at project root
- `.claude/` with agents, commands, docs
- `tools/` directory structure ready
- `.gitignore` with common patterns

Then customize:
1. Edit `CLAUDE.md` for project-specific context
2. Edit `.claude/docs/ARCHITECTURE_GUIDE.md` for your architecture

## Structure

```
your-project/                    # This repo clones as your project root
├── CLAUDE.md                    # Project guidelines
├── .gitignore                   # Common ignores + Claude settings
├── .claude/
│   ├── agents/                  # 9 specialized agents
│   ├── commands/                # 12 slash commands
│   └── docs/                    # Detailed guides
└── tools/
    ├── README.md                # Tools documentation
    ├── scripts/                 # Utility scripts
    ├── testing/
    │   ├── requests/            # QA test requests for Antigravity
    │   └── results/             # Test reports (gitignored)
    └── tmp/                     # Disposable files (gitignored)
```

## Commands (12)

| Command | Purpose |
|---------|---------|
| `/start` | Load project context (principles, architecture) |
| `/spec` | Create strategic implementation plan (saves to `.claude/specs/`) |
| `/delegate` | Delegate to agents, keep context clean |
| `/orchestrate` | Full workflow: analyze → implement → test → document → review |
| `/debug` | First-principles debugging |
| `/poc` | Proof of concept to validate technical feasibility |
| `/commit` | Smart git commits with logical grouping |
| `/docs-update` | Analyze and update documentation |
| `/test` | Create QA test requests for Antigravity |
| `/kiro` | Execute tasks from Kiro implementation plans |
| `/kiro-create` | Create new Kiro specs (requirements.md, design.md, tasks.md) |
| `/kiro-review` | Review completed Kiro tasks against specifications |

## Kiro IDE Integration

[Kiro](https://kiro.dev) is Amazon's spec-driven development IDE. This skeleton includes commands to work with Kiro-style specifications from Claude Code:

- **`/kiro-create <feature>`**: Generate a complete Kiro spec with requirements.md (user stories, EARS acceptance criteria), design.md (architecture, interfaces, correctness properties), and tasks.md (numbered implementation checklist)
- **`/kiro <task_number>`**: Execute a specific task from a Kiro tasks.md file following the standardized workflow
- **`/kiro-review <task_number>`**: Review completed tasks against specifications to verify correctness

Specs are stored in `.kiro/specs/{feature-name}/` following Kiro conventions.

**Cross-agent reviews**: The `/kiro-review` command is designed for a separate Claude session (or other AI agents like OpenAI Codex) to review completed work. This provides an independent verification layer - the implementing agent and reviewing agent are different sessions, reducing bias and catching issues the implementer might miss.

## Antigravity Testing

[Google Antigravity](https://antigravity.google/) is Google's agentic development platform that can test applications using browser automation - clicking buttons and typing text like a real human user.

The `/test` command creates human-readable QA test request documents that Antigravity can execute:
- Exhaustively specific test cases with exact steps
- Pre-conditions and expected results
- Browser console and terminal monitoring instructions
- Report templates for testers to fill out

Test requests are saved to `tools/testing/requests/`.

## Agents (9)

| Agent | Purpose | Model |
|-------|---------|-------|
| `strategic-planner` | Implementation planning | opus |
| `senior-dev-consultant` | Expert advice, architecture | opus |
| `senior-dev-implementer` | Production-quality code | sonnet |
| `task-completion-validator` | Verify task completeness | sonnet |
| `investigator` | Deep research, bug hunting | sonnet |
| `codebase-analyzer` | Understand project structure | sonnet |
| `docs-explorer` | Documentation research | sonnet |
| `test-generator` | Create test suites | sonnet |
| `docs-maintainer` | Update documentation | sonnet |

## Docs

| Doc | Purpose | Portable? |
|-----|---------|-----------|
| `CODE_PRINCIPLES.md` | Code quality hard limits (50 line functions, etc.) | Yes |
| `FILE_PRINCIPLES.md` | File organization standards | Yes |
| `ARCHITECTURE_GUIDE.md` | Project architecture patterns | **Customize** |
| `DELEGATE_GUIDE.md` | Agent delegation strategies | Yes |
| `DEBUG_GUIDE.md` | Debugging methodology | Yes |
| `POC_GUIDE.md` | Proof of concept patterns | Yes |
| `SPEC_GUIDE.md` | Strategic planning workflow | Yes |
| `COMMIT_GUIDELINES.md` | Commit message standards | Yes |
| `TESTING_REQUEST_GUIDE.md` | QA test request format for Antigravity | Yes |
| `KIRO_SPEC_GUIDE.md` | Kiro spec writing guidelines | Yes |
| `KIRO_TASK_EXECUTION_GUIDE.md` | Kiro task execution workflow | Yes |
| `KIRO_REVIEW_GUIDE.md` | Kiro task review process | Yes |
| `KIRO_TESTING_GUIDE.md` | Testing approach for Kiro tasks | Yes |

## Key Principles

### Code Quality (Enforced)

| Rule | Limit |
|------|-------|
| Function length | Max 50 lines |
| Parameters | Max 4 (use object for more) |
| Nesting depth | Max 4 levels |
| File length | Max 300 lines |

### File Organization

- One responsibility per file
- Group by feature, not type
- Naming: `user.service.ts`, `auth.middleware.ts`
- Temp files in `tools/tmp/` only

## Workflow Patterns

### Context-Preserving Development
```
/start                    # Load context
/delegate investigate X   # Agent investigates
/orchestrate feature Y    # Full workflow with agents
/commit                   # Smart commit
```

### Kiro Spec-Driven Development
```
/kiro-create user-auth    # Create specs for user authentication
/kiro 1.1                 # Execute first task
/kiro-review 1.1          # Review completed task
/kiro 1.2                 # Continue to next task
```

### QA Testing with Antigravity
```
/test login-flow          # Create test request document
# Antigravity executes the test cases in browser
# Review results in tools/testing/results/
```

## Customization

### Add Custom Agent
Create `.claude/agents/my-agent.md`:
```yaml
---
name: my-agent
description: When to use this agent
tools: Bash, Read, Write, Edit
model: sonnet
---

**FIRST**: Read `.claude/commands/start.md` and follow its instructions.

Your agent instructions here...
```

### Add Custom Command
Create `.claude/commands/my-command.md`:
```yaml
---
argument-hint: <args>
description: What this command does
---

## Task
Instructions for the command...
```

## Philosophy

- **Minimal CLAUDE.md**: ~85 lines, references docs
- **Enforceable Principles**: Hard limits, not suggestions
- **Agent Delegation**: Keep main context clean
- **Portable by Default**: Customize only ARCHITECTURE_GUIDE
- **IDE Interoperability**: Works with Kiro specs and Antigravity testing
