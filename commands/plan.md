---
argument-hint: [description of what to plan] (required: description of the feature or system to plan)
description: Create comprehensive strategic plan for complex features using highest-capability planning agent
---

# Plan Command

## Purpose
Generate detailed, strategic implementation plans for complex features or major undertakings. Uses the most capable model (Opus) to think deeply about architecture, risks, dependencies, and create actionable roadmaps.

## Command Syntax
- `/plan Build a real-time notification system with websockets`
- `/plan Migrate our REST API to GraphQL`
- `/plan Refactor authentication to use OAuth 2.0`
- `/plan [description of what needs to be planned]`

## User Instructions Processing

**Arguments received:** `$ARGUMENTS`

The entire argument string is the description of what needs to be planned.
The agent will extract a suitable feature name from the description.

## Command Instructions

### 1. Validate Input

```bash
# Check if .claude/plans directory exists
if [ ! -d ".claude/plans" ]; then
  echo "📁 Creating .claude/plans directory..."
  mkdir -p .claude/plans
fi

# Parse arguments
if [ -z "$ARGUMENTS" ]; then
  echo "❌ Please provide a description of what to plan"
  echo "Usage: /plan Build a real-time notification system"
  exit 1
fi

# Use the full argument as description
DESCRIPTION="$ARGUMENTS"

# Generate feature name from description (agent will do this more intelligently)
FEATURE_NAME=$(echo "$DESCRIPTION" | cut -d' ' -f1-3 | tr '[:upper:]' '[:lower:]' | tr ' ' '-')

echo "📋 Planning: $DESCRIPTION"
echo "📁 Plan will be saved as: ${FEATURE_NAME}-plan.md"
```

### 2. Check Existing Plans

```bash
# Check if plan already exists
PLAN_FILE=".claude/plans/${FEATURE_NAME}-plan.md"

if [ -f "$PLAN_FILE" ]; then
  echo "⚠️  Existing plan found: $PLAN_FILE"
  echo "Options:"
  echo "1. View existing plan"
  echo "2. Update existing plan"
  echo "3. Create new plan (will archive old)"

  # Handle user choice
  # If updating, load existing plan for context
  # If new, archive old as .bak
fi
```

### 3. Gather Context

Before invoking the strategic-planner agent, gather relevant context:

```bash
# Analyze current project state
echo "🔍 Analyzing project context..."

# Get project structure overview
find . -type f -name "*.md" | grep -E "(README|ARCHITECTURE|API)" | head -5

# Check for related existing code
echo "🔍 Checking for related existing features..."
grep -r "$FEATURE_NAME" --include="*.js" --include="*.ts" --include="*.py" . 2>/dev/null | head -5

# Get recent related commits
git log --grep="$FEATURE_NAME" --oneline -5 2>/dev/null

# Check TODO items
grep -r "TODO" --include="*.js" --include="*.ts" --include="*.py" . 2>/dev/null | grep -i "${FEATURE_NAME}" | head -5
```

### 4. Invoke Strategic Planner Agent

```
"I'll use the strategic-planner agent to create a comprehensive implementation plan."

[Invoke strategic-planner agent with:
- Description: $DESCRIPTION
- Context: Current project state, architecture, existing patterns
- Requirements: Any specific constraints or requirements mentioned
- Task: Generate appropriate feature name from description
- Output location: .claude/plans/[generated-name]-plan.md]

The agent should:
1. Extract a suitable feature name from the description
2. Analyze the full scope of work
3. Research existing patterns in the codebase
4. Create comprehensive plan document
5. Generate TODO items for immediate tasks
6. Save plan with appropriate naming
```

### 5. Post-Planning Actions

After the agent creates the plan:

```bash
# Confirm plan creation
if [ -f "$PLAN_FILE" ]; then
  echo "✅ Strategic plan created successfully!"
  echo "📁 Location: $PLAN_FILE"

  # Show plan summary
  echo ""
  echo "📊 Plan Overview:"
  grep "^##" "$PLAN_FILE" | head -10

  # Extract immediate tasks
  echo ""
  echo "🎯 Immediate Actions:"
  grep "^- \[ \]" "$PLAN_FILE" | head -5

  # Create TODO items
  echo ""
  echo "✅ TODO items generated for Phase 1"

  # Suggest next steps
  echo ""
  echo "📌 Next Steps:"
  echo "1. Review the complete plan at $PLAN_FILE"
  echo "2. Confirm approach and estimates"
  echo "3. Begin implementation with first TODO items"
  echo "4. Run '/status' to see generated TODOs"
else
  echo "❌ Failed to create plan"
fi
```

### 6. Plan Management Features

**Update Existing Plan:**
```bash
/plan update feature-name
# Loads existing plan and updates based on progress
```

**View Plan Status:**
```bash
/plan status feature-name
# Shows completion percentage, current phase, blockers
```

**Archive Completed Plan:**
```bash
/plan archive feature-name
# Moves to .claude/plans/archive/
```

## Integration Points

### With TodoWrite Tool
The strategic-planner agent should:
1. Generate TODO items for immediate tasks
2. Structure TODOs by phase
3. Include context in TODO descriptions

### With Other Agents
The plan should specify when to use:
- `senior-dev-consultant` for architecture reviews
- `codebase-analyzer` for research phases
- `test-generator` for test planning
- `task-completion-validator` for phase completion

### With Documentation
Plans should:
- Reference architecture guides
- Link to relevant docs
- Suggest documentation updates needed

## Plan Lifecycle

### 1. Creation
- User runs `/plan feature-name "description"`
- Strategic-planner creates comprehensive plan
- Plan saved to `.claude/plans/`
- TODO items generated

### 2. Execution
- Developers work through TODO items
- Plan referenced during implementation
- Progress tracked in plan document

### 3. Maintenance
- Plan updated as work progresses
- New discoveries added
- Estimates adjusted based on reality
- Completed items checked off

### 4. Review Points
- Phase completion reviews
- Architecture validation
- Risk reassessment
- Approach adjustments

### 5. Completion
- Final validation
- Lessons learned documented
- Plan archived for reference

## Example Usage

**User says:** `/plan Build real-time notification system with websockets`

**Result:**
```
📋 Planning: Build real-time notification system with websockets
📁 Plan will be saved as: real-time-notification-plan.md

🔍 Analyzing project context...
[Context gathering output]

🧠 Invoking strategic planning agent (Opus model)...

✅ Strategic plan created successfully!
📁 Location: .claude/plans/notification-system-plan.md

📊 Plan Overview:
## Overview
## Requirements
## Technical Approach
## Implementation Phases
### Phase 1: Foundation (3 days)
### Phase 2: Core Implementation (5 days)
### Phase 3: Integration & Testing (2 days)
## Risk Analysis
## Testing Strategy

🎯 Immediate Actions:
- [ ] Set up WebSocket server infrastructure
- [ ] Create NotificationService base class
- [ ] Design notification message schema
- [ ] Implement connection manager
- [ ] Add authentication to WebSocket

✅ TODO items generated for Phase 1

📌 Next Steps:
1. Review the complete plan at .claude/plans/notification-system-plan.md
2. Confirm approach and estimates
3. Begin implementation with first TODO items
4. Run '/status' to see generated TODOs
```

## Error Handling

```bash
# Handle missing dependencies
if ! command -v git &> /dev/null; then
  echo "⚠️  Git not found - some context gathering skipped"
fi

# Handle permission issues
if [ ! -w ".claude/plans" ]; then
  echo "❌ Cannot write to .claude/plans directory"
  echo "Please check permissions"
  exit 1
fi

# Handle agent failures
if [ $AGENT_RESULT -ne 0 ]; then
  echo "❌ Strategic planning failed"
  echo "Try simplifying the description or breaking into smaller features"
fi
```

## Plan Quality Checklist

Before finalizing a plan, ensure:
- [ ] Clear phases with dependencies
- [ ] Realistic time estimates
- [ ] Identified risks and mitigations
- [ ] Testing strategy included
- [ ] Rollback plan defined
- [ ] Success criteria specified
- [ ] Integration points identified
- [ ] Documentation needs noted

---

This command leverages the highest-capability model to create thoughtful, comprehensive plans that guide development from start to finish, reducing uncertainty and improving project outcomes.