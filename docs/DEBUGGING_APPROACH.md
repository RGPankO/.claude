# Debugging Approach Guide

> **Context Loading**: This file is REFERENCED, not loaded. Main Claude should refer to this when debugging issues.

## Debugging Philosophy

Debugging is a systematic process of understanding why code behaves differently than expected. The goal is to find the root cause quickly and fix it properly, not just patch symptoms.

## The Scientific Method of Debugging

1. **Observe**: What is the actual behavior?
2. **Hypothesize**: What could cause this behavior?
3. **Predict**: If hypothesis is true, what else would we see?
4. **Test**: Verify predictions with minimal tests
5. **Conclude**: Confirm or reject hypothesis
6. **Repeat**: Until root cause is found

## Debugging Strategies

### 1. Reproduce First
- Never debug what you can't reproduce
- Create minimal reproduction case
- Document exact steps to reproduce
- Note environment and conditions
- Save reproduction as a test case

### 2. Binary Search
- Divide problem space in half
- Test which half contains the bug
- Repeat until bug is isolated
- Works for: commits, code blocks, data sets

### 3. Rubber Duck Debugging
- Explain the code line by line
- Often reveals faulty assumptions
- Document your understanding
- Question every assumption

### 4. Differential Debugging
- Compare working vs broken states
- What changed between them?
- Use git bisect for commit-level
- Compare configurations
- Diff data inputs/outputs

## Quick Debugging Techniques

### Print Debugging
```javascript
console.log('🔍 DEBUG: value =', value);
console.log('📍 CHECKPOINT: reached here');
console.log('❌ ERROR STATE:', error);
```

**Best Practices**:
- Use distinctive prefixes
- Include variable names
- Show data types and shapes
- Clean up after fixing

### Minimal Test Files
Create isolated test files to verify behavior:
```javascript
// debug-test.js
const problematicFunction = require('./module');

// Minimal test case
const input = { /* minimal data */ };
const result = problematicFunction(input);
console.log('Result:', result);
```

### Browser DevTools
- **Console**: Log outputs and errors
- **Debugger**: Set breakpoints
- **Network**: Check API calls
- **Performance**: Find bottlenecks
- **Memory**: Detect leaks

### Error-First Approach
1. Check error messages carefully
2. Read stack traces from bottom up
3. Identify the failing line
4. Trace back through call stack
5. Find where bad data originated

## Common Bug Categories

### Logic Errors
- Off-by-one errors
- Incorrect conditionals
- Wrong operator usage
- Faulty algorithms
- **Debug**: Add assertions, trace execution

### State Management
- Race conditions
- Stale closures
- Unintended mutations
- Shared mutable state
- **Debug**: Log state changes, use immutable data

### Type Errors
- Null/undefined access
- Type mismatches
- Implicit conversions
- **Debug**: Add type checks, use TypeScript

### Async Issues
- Unhandled promises
- Callback hell
- Race conditions
- Timeout issues
- **Debug**: Log async flows, use async/await

### Integration Problems
- API contract violations
- Version mismatches
- Environment differences
- Configuration issues
- **Debug**: Check versions, validate inputs/outputs

## Systematic Debugging Process

### 1. Gather Information
- Error messages and stack traces
- Recent changes (git log)
- Environment details
- User reports
- System logs

### 2. Form Hypothesis
- What could cause these symptoms?
- List possible causes
- Rank by probability
- Consider recent changes
- Check similar past issues

### 3. Test Hypothesis
- Create minimal test
- Isolate the problem
- Change one thing at a time
- Document what you try
- Keep tests for regression

### 4. Fix and Verify
- Fix root cause, not symptoms
- Write test to prevent regression
- Verify fix doesn't break other things
- Document the solution
- Clean up debug code

## Performance Debugging

### Profiling First
- Measure before optimizing
- Find actual bottlenecks
- Use profiling tools
- Check memory usage
- Monitor network calls

### Common Performance Issues
- **N+1 Queries**: Batch database calls
- **Memory Leaks**: Clean up references
- **Blocking Operations**: Use async
- **Excessive Rendering**: Optimize updates
- **Large Bundles**: Code splitting

## Memory Debugging

### Detecting Memory Leaks
- Monitor heap size over time
- Take heap snapshots
- Compare snapshots
- Find retained objects
- Trace reference chains

### Common Causes
- Event listeners not removed
- Timers not cleared
- Closures holding references
- Global variables
- Circular references

## Debugging Tools

### Language-Specific
**JavaScript/Node.js**:
- Chrome DevTools
- node --inspect
- ndb debugger
- why-is-node-running

**Python**:
- pdb debugger
- ipdb (enhanced)
- py-spy profiler
- memory_profiler

**General**:
- IDE debuggers
- Log aggregators
- APM tools
- Error tracking (Sentry)

### Debugging Commands
```bash
# Git bisect to find breaking commit
git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>

# Find which process uses a port
lsof -i :3000

# Monitor file changes
watch -n 1 'tail -20 app.log'

# Check memory usage
top -o MEM

# Network debugging
curl -v https://api.example.com
```

## Anti-Patterns to Avoid

1. **Shotgun Debugging**: Changing random things hoping it works
2. **Ignoring Error Messages**: They often point to the exact problem
3. **Not Reading Documentation**: The answer might be there
4. **Debugging in Production**: Use proper environments
5. **Not Taking Breaks**: Fresh eyes find bugs faster
6. **Pride Over Pragmatism**: Ask for help when stuck

## When to Ask for Help

- After 30-60 minutes if completely stuck
- When you need domain expertise
- For critical production issues
- When multiple people are affected
- Before making architectural changes

## Post-Debugging Actions

1. **Write a Test**: Prevent regression
2. **Document the Fix**: Help future debuggers
3. **Share Knowledge**: Update docs if needed
4. **Clean Up**: Remove debug code
5. **Post-Mortem**: Learn from significant bugs

## Debugging Mantras

- "The bug is always in your code, not the framework"
- "Read the error message again"
- "What changed recently?"
- "Make it fail consistently first"
- "One change at a time"
- "Trust but verify"