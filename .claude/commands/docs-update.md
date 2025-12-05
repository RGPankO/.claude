---
argument-hint: [check | update | suggest] (optional: check for needed updates, update docs, or suggest improvements)
description: Analyze code changes and update documentation to keep CLAUDE.md and .claude/docs/ current
---

# Docs Update Command

## Purpose
Maintain documentation currency by analyzing recent changes and updating CLAUDE.md and .claude/docs/ files to reflect new patterns, lessons learned, and evolved practices.

## Command Syntax
- `/docs-update` - Check and update documentation based on recent changes
- `/docs-update check` - Only check what needs updating (no changes)
- `/docs-update update` - Force update all documentation
- `/docs-update suggest` - Suggest improvements without making changes
- `/docs-update [specific-topic]` - Update documentation for specific area

## User Instructions Processing

**Arguments received:** `$ARGUMENTS`

Parse `$ARGUMENTS` for operations:
- Default/no args: Check and update as needed
- `check`: Analysis only, report needed updates
- `update`: Force comprehensive update
- `suggest`: Provide improvement suggestions
- Specific topic: Focus on particular area (e.g., "architecture", "testing")

## Command Instructions

### 1. Analyze Current State

```bash
# Check recent changes
echo "📊 Analyzing recent changes..."

# Get recent commits for context
git log --oneline -20 --pretty=format:"%h %s" 2>/dev/null || echo "No git history"

# Find recently modified files
find . -type f -name "*.md" -mtime -7 2>/dev/null | grep -E "CLAUDE|\.claude/docs" || echo "No recent doc changes"

# Check for TODOs in code that might need documentation
grep -r "TODO\|FIXME" --include="*.js" --include="*.ts" --include="*.py" . 2>/dev/null | grep -i "doc" | head -5
```

### 2. Identify Documentation Needs

**Check for Common Triggers:**
- New patterns used 3+ times
- Repeated bugs or issues
- New agents or commands added
- Architectural changes
- Workflow improvements
- Lessons learned from debugging

### 3. Documentation Health Check

```bash
# Check CLAUDE.md size (should be under 500 lines)
echo "📏 Checking CLAUDE.md size..."
wc -l CLAUDE.md 2>/dev/null || wc -l .claude/CLAUDE.md 2>/dev/null

# Check if all referenced docs exist
echo "🔗 Checking documentation references..."
for doc in ARCHITECTURE_GUIDE TESTING_STRATEGY DEBUGGING_APPROACH AGENT_WORKFLOWS CONTEXT_MANAGEMENT; do
  [ -f ".claude/docs/${doc}.md" ] && echo "✅ ${doc}.md exists" || echo "❌ ${doc}.md missing"
done

# Check for broken references
echo "🔍 Checking for broken references..."
grep -h "\.claude/docs/" CLAUDE.md .claude/CLAUDE.md 2>/dev/null | while read -r line; do
  ref=$(echo "$line" | grep -oE "\.claude/docs/[A-Z_]+\.md")
  [ -f "$ref" ] || echo "❌ Broken reference: $ref"
done
```

### 4. Use Documentation Maintainer Agent

When updates are needed, invoke the docs-maintainer agent:

```
"I'll use the docs-maintainer agent to analyze and update the documentation based on recent changes."

[Invoke docs-maintainer agent with context about:
- Recent changes
- Identified patterns
- Documentation gaps
- Specific focus areas from arguments]
```

### 5. Documentation Update Process

**For Check Mode (`check`):**
```
DOCUMENTATION STATUS REPORT
=========================

📊 Current State:
  CLAUDE.md: 423 lines (✅ under 500)
  Docs files: 5/5 present
  Last updated: 2 days ago

🔍 Needs Attention:
  - New pattern detected: Event-driven messaging (used 4 times)
  - Undocumented workflow: Parallel agent execution
  - Stale section: Testing approach (framework changed)

💡 Recommendations:
  1. Update ARCHITECTURE_GUIDE.md with messaging pattern
  2. Add parallel execution to AGENT_WORKFLOWS.md
  3. Update test framework in TESTING_STRATEGY.md

Run '/docs-update update' to apply changes
```

**For Update Mode (`update`):**
```
DOCUMENTATION UPDATE IN PROGRESS
================================

🔄 Updating documentation...

[Invoke docs-maintainer agent]

✅ Updates Complete:
  - CLAUDE.md: Added new agent reference
  - ARCHITECTURE_GUIDE.md: Added event-driven pattern
  - AGENT_WORKFLOWS.md: Added parallel execution workflow
  - TESTING_STRATEGY.md: Updated framework information

📝 Changes Summary:
  Files modified: 4
  Lines added: 127
  Lines removed: 43
  New sections: 3

✨ Documentation is now current!
```

**For Suggest Mode (`suggest`):**
```
DOCUMENTATION IMPROVEMENT SUGGESTIONS
====================================

Based on analysis, consider:

📚 Content Improvements:
  1. Add troubleshooting section to DEBUGGING_APPROACH.md
  2. Include performance benchmarks in patterns
  3. Add decision log template to ARCHITECTURE_GUIDE.md

🎯 Organization Improvements:
  1. Move detailed agent info from CLAUDE.md to AGENT_WORKFLOWS.md
  2. Create quick reference card in CLAUDE.md
  3. Add table of contents to longer docs

✏️ Clarity Improvements:
  1. Add more examples to abstract concepts
  2. Include diagrams for complex workflows
  3. Clarify when to use each agent

💡 New Documentation Needs:
  1. Create PERFORMANCE_GUIDE.md for optimization patterns
  2. Add SECURITY_PRACTICES.md for security patterns
  3. Consider TROUBLESHOOTING.md for common issues
```

### 6. Maintain Documentation Principles

**Keep CLAUDE.md Concise:**
- Under 500 lines total
- Quick reference only
- Links to detailed docs
- Project-specific context
- Essential commands/agents

**Detailed Docs Philosophy:**
- Referenced, not loaded
- Comprehensive coverage
- Pattern-focused
- Technology-agnostic
- Example-driven

### 7. Auto-Detection Patterns

The command should detect:

```bash
# Pattern repetition (potential new convention)
echo "🔄 Checking for repeated patterns..."
# Look for similar code structures appearing multiple times

# New tools or commands
echo "🛠️ Checking for new tools..."
ls -la commands/ agents/ 2>/dev/null | grep -E "\.md$" | wc -l

# Architecture changes
echo "🏗️ Checking for architecture changes..."
# Look for new directories, moved files, refactoring

# Error patterns (lessons learned)
echo "🐛 Checking error patterns..."
# Analyze recent bug fixes for patterns
```

## Integration with Development Flow

### Automatic Triggers

Include in CLAUDE.md instructions:
```markdown
## Documentation Maintenance

⚠️ **Important**: After significant changes, consider running `/docs-update check` to ensure documentation remains current.

Triggers for documentation review:
- Implementing new architectural patterns
- Completing major features
- Resolving complex bugs
- Establishing new conventions
- Adding agents or commands
```

### Proactive Maintenance

When main Claude detects patterns:
1. Notice repeated patterns or issues
2. Suggest running `/docs-update check`
3. Use docs-maintainer agent if needed
4. Keep documentation valuable

## Output Examples

**User says:** "/docs-update"
**Result:** Checks for needed updates and applies them automatically

**User says:** "/docs-update check"
**Result:** Reports what needs updating without making changes

**User says:** "/docs-update suggest"
**Result:** Provides improvement recommendations

**User says:** "/docs-update architecture"
**Result:** Focuses on architecture documentation updates

## Quality Checks

Before completing updates:
- [ ] CLAUDE.md remains under 500 lines
- [ ] All references are valid
- [ ] Documentation is technology-agnostic
- [ ] Examples are clear and relevant
- [ ] No contradictions between docs
- [ ] Patterns are actionable
- [ ] Project-specific sections marked

## Error Handling

```bash
# Handle missing documentation gracefully
if [ ! -f "CLAUDE.md" ] && [ ! -f ".claude/CLAUDE.md" ]; then
  echo "❌ No CLAUDE.md found"
  echo "💡 This command requires Claude documentation structure"
  echo "   Run this in a project with .claude/ setup"
  exit 1
fi

# Handle no changes needed
if [ -z "$UPDATES_NEEDED" ]; then
  echo "✅ Documentation is current!"
  echo "No updates needed at this time."
fi
```

---

This command ensures documentation evolves with the codebase, capturing lessons learned and maintaining useful, current guidance for development.