# Agent Testing Guide

## ⚡ CRITICAL: NO SERVER STARTUP REQUIRED

**Development server is managed by user - agents NEVER start servers**

## 🎯 Pre-Test Protocol

### 1. URL Verification
- ✅ **Test URL**: Use URL provided by main Claude (never assume)
- ✅ **Pre-flight**: Verify URL responds before testing (5 seconds max)
- ❌ **DO NOT**: Run server commands, install dependencies, or assume URLs
- ❌ **DO NOT**: Hardcode localhost or port numbers

### 2. If Server Issues
- **Connection Refused**: Report to human immediately - do not troubleshoot
- **404 Error**: Report URL and error - do not attempt fixes
- **Slow Response**: Document but continue testing

## 📁 File Output Restrictions

### CRITICAL: Only write to .temp/ directory
```
.temp/
├── test-results/          # Screenshots and reports
├── test-screenshots/      # Visual evidence
├── playwright-reports/    # Automated test outputs
└── scratch/              # Temporary working files
```

### File Creation Rules
- ✅ **Allow**: Write reports, screenshots, logs to .temp/
- ❌ **Block**: Create test files (.spec.js) anywhere
- ❌ **Block**: Echo commands for file creation
- ❌ **Block**: Write outside .temp/ directory

## 🔍 Testing Protocol

### Standard Agent Flow
1. **Pre-flight**: Verify provided URL responds (5 seconds max)
2. **Navigate**: Go to test URL provided by main Claude
3. **Execute**: Run specific test scenario as instructed
4. **Document**: Screenshots + findings to .temp/test-results/
5. **Report**: Structured findings with actionable recommendations

### Agent Responsibilities
- **Execute tests** as specified by main Claude
- **Report findings** with screenshots and detailed steps
- **Document issues** with clear reproduction steps
- **Focus only** on assigned test scope

### What Agents Do NOT Do
- Start or manage servers
- Decide what to test (main Claude decides)
- Create permanent test files
- Make assumptions about application structure
- Troubleshoot infrastructure issues

## 📊 Efficiency Guidelines

### Time Allocation
- **Setup**: 0-5 seconds (just URL verification)
- **Testing**: 85% of time on actual feature testing
- **Documentation**: 10% on screenshots and findings
- **Infrastructure**: 5% maximum on any setup issues

### Success Criteria
- ✅ Immediate testing start (no setup delays)
- ✅ Comprehensive coverage of assigned scope
- ✅ Clear pass/fail determinations
- ✅ Actionable bug reports with reproduction steps
- ✅ Visual evidence supporting findings

## 🔄 Error Handling

### Standard Error Response
- **URL unreachable**: Report to human, do not retry
- **Console errors**: Document and continue testing
- **Performance issues**: Note but continue testing
- **Infrastructure problems**: Report immediately, do not fix

### Focus Areas Only
- Core feature functionality as specified
- User interaction flows as assigned
- Visual feedback systems as requested
- Business logic validation as instructed

**Goal**: Efficient, focused testing based on main Claude's specific instructions without infrastructure assumptions or scope creep.