# Claude Code Skeleton

A portable, optimized skeleton for Claude Code projects with enforceable principles, specialized agents, and minimal commands.

## Purpose

This skeleton provides:
- **Enforceable Standards**: CODE_PRINCIPLES and FILE_PRINCIPLES with hard limits
- **Specialized Agents**: 9 agents for delegation, keeping main context clean
- **Minimal Commands**: 8 commands that reference detailed docs
- **~90% Portable**: Only ARCHITECTURE_GUIDE needs project customization

## Quick Start

```bash
cd your-project
git clone https://github.com/RGPankO/.claude.git .claude
mv .claude/CLAUDE.md ./
```

Then customize:
1. Edit `CLAUDE.md` for project-specific context
2. Edit `.claude/docs/ARCHITECTURE_GUIDE.md` for your architecture
3. Optionally add `.claude/docs/KIRO_TASK_EXECUTION_GUIDE.md` workflow

## Structure

```
your-project/
├── CLAUDE.md                    # Project guidelines (move to root)
├── tools/                       # Create this directory structure
│   ├── scripts/                 # Utility scripts
│   ├── testing/requests/        # QA test requests
│   └── tmp/                     # Disposable files (gitignored)
└── .claude/
    ├── .gitignore               # Ignores settings.local.json
    ├── agents/                  # 9 specialized agents
    ├── commands/                # 8 slash commands
    └── docs/                    # Detailed guides
```

## Commands (8)

| Command | Purpose |
|---------|---------|
| `/start` | Load project context (principles, architecture) |
| `/plan` | Create implementation plan (saves to tools/tmp/) |
| `/delegate` | Delegate to agents, keep context clean |
| `/orchestrate` | Full workflow: analyze → implement → test → document → review |
| `/debug` | First-principles debugging |
| `/commit` | Smart git commits |
| `/docs-update` | Update documentation |
| `/test` | Create QA test requests |

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
| `PLAN_GUIDE.md` | Planning workflow | Yes |
| `COMMIT_GUIDELINES.md` | Commit message standards | Yes |
| `AGENT_TESTING_GUIDE.md` | QA agent protocols | Yes |
| `KIRO_TASK_EXECUTION_GUIDE.md` | Task execution workflow | Yes |

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

### Mini-Delegation (for large tasks)
```
1. Investigate → get findings
2. Implement backend → review
3. Implement frontend → review
4. Add tests → complete
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
