# QA Testing Protocols

## Automated Browser Testing

**Playwright QA Tester Agent**: Use the `playwright-qa-tester` agent for comprehensive browser testing.

### When to Invoke
- After implementing new features, fixing bugs, or making changes that need validation
- For functionality verification, bug detection, console error monitoring, performance assessment
- Regression testing after changes
- Pre-deployment verification

### Setup Requirements
- **Server Status**: Development server managed by user (check `.env` for PORT)
- **Test URL**: Main Claude provides complete URL to agent
- **No Manual Setup**: Agent should use provided URL, never start servers

### Critical Workflow - Bug Fix Validation
1. **Before fixing**: Use playwright-qa-tester to identify and document the bug
2. **After fixing**: ALWAYS re-run playwright-qa-tester to confirm the fix worked
3. **Verification Required**: Never consider a bug fix complete without agent confirmation
4. **Documentation**: Agent provides before/after comparison and validation evidence

### Efficiency Protocol
- **Server Status**: Development server managed by user (assume running)
- **QA Agents**: DO NOT start servers - use existing server
- **Pre-flight Check**: Agents must verify server responds before testing
- **If Connection Error**: Report to user immediately - do not attempt server startup
- **Known Issues**: Minor errors may be expected - ignore if non-blocking
- **Test Duration**: Focus on testing, not infrastructure setup
- **File Restrictions**: Limit file writes to designated test output directories

### Output Locations
- **Screenshots**: `.temp/test-results/`
- **Reports**: `.temp/test-screenshots/`
- **Logs**: Browser console outputs

**See `AGENT_TESTING_GUIDE.md` for comprehensive agent testing protocols.**