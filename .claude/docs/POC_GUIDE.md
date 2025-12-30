# Proof of Concept (PoC) Guide

> **Purpose**: Validate technical feasibility and assumptions before committing to full implementation.

## Why PoC First?

**De-risk development** by testing unknowns early:
- Validate that a technology/API works as expected
- Confirm integration approaches before building around them
- Catch "this won't work" issues before investing significant effort
- Build confidence in the technical approach

**PoC Philosophy**: It's cheaper to spend 30 minutes proving something works than 3 days building around something that doesn't.

## What Makes a Good PoC Candidate?

### High-Value PoC Targets

| Category | Examples | Why PoC? |
|----------|----------|----------|
| External APIs | YouTube API, payment gateways, AI services | Rate limits, auth complexity, response formats unknown |
| New Libraries | PDF parsing, video processing, web scraping | May not work as documented, edge cases |
| Browser APIs | WebRTC, WebGL, Service Workers | Browser support, permission handling |
| Data Processing | Large file handling, streaming, formats | Performance, memory, encoding issues |
| Integration Points | OAuth flows, webhooks, SSO | Complex handshakes, timing issues |

### PoC Selection Criteria

**Good PoC candidates have:**
- Technical uncertainty (never done before, new API/library)
- High dependency (other features rely on this working)
- Hard to change later (architectural decision)
- Unknown performance characteristics

**Skip PoC for:**
- Well-known patterns you've implemented before
- Standard CRUD operations
- UI styling and layout
- Business logic (test with unit tests instead)

## PoC Structure

### Characteristics of a Good PoC

```
MINIMAL          - Smallest code that proves the concept
ISOLATED         - No dependencies on your main codebase
THROWAWAY        - Code quality doesn't matter, learning does
FOCUSED          - Tests ONE thing, not multiple
MEASURABLE       - Clear success/failure criteria
TIME-BOXED       - Set a limit (30min-2hrs typical)
```

### PoC File Location

```
tools/
└── tmp/
    └── poc/
        ├── youtube-subtitles-poc.html    # Browser-based PoC
        ├── pdf-parsing-poc.ts            # Script-based PoC
        └── webhook-receiver-poc.js       # Server-based PoC
```

**Note**: `tools/tmp/` is gitignored - PoCs are disposable.

## PoC Types

### 1. Browser PoC (Single HTML File)
Best for: APIs, UI concepts, client-side processing

```html
<!DOCTYPE html>
<html>
<head><title>PoC: [Feature Name]</title></head>
<body>
  <h1>PoC: [What we're testing]</h1>

  <!-- Minimal UI for input/output -->
  <input id="input" placeholder="Test input">
  <button onclick="test()">Test</button>
  <pre id="output"></pre>

  <script>
    async function test() {
      const input = document.getElementById('input').value;
      const output = document.getElementById('output');

      try {
        // PoC code here
        const result = await doTheThing(input);
        output.textContent = JSON.stringify(result, null, 2);
      } catch (e) {
        output.textContent = 'ERROR: ' + e.message;
      }
    }

    async function doTheThing(input) {
      // Minimal implementation to prove concept
    }
  </script>
</body>
</html>
```

### 2. Script PoC (Node/Python/etc)
Best for: File processing, server-side operations, CLI tools

```javascript
// poc-[feature-name].js
// PoC: [What we're testing]
// Success criteria: [What proves it works]

async function main() {
  console.log('Starting PoC...');

  try {
    // Minimal implementation
    const result = await doTheThing();

    console.log('SUCCESS:', result);
  } catch (e) {
    console.error('FAILED:', e.message);
    process.exit(1);
  }
}

main();
```

### 3. Integration PoC
Best for: Multi-service workflows, auth flows, webhooks

```javascript
// Tests the handshake between systems
// Often requires two scripts or a mini-server
```

## PoC Workflow

### Step 1: Identify the Unknown
```
Q: What don't we know?
Q: What could fail?
Q: What have we never done before?
```

### Step 2: Define Success Criteria
```
SUCCESS means:
- [ ] API returns expected data format
- [ ] Processing completes in < 5 seconds
- [ ] Output matches expected structure
```

### Step 3: Time-Box the Attempt
```
LIMIT: 1 hour maximum
If not working by then: reassess approach
```

### Step 4: Build Minimal PoC
```
- Single file when possible
- Hardcoded values OK
- No error handling needed
- No tests needed
- Comments optional
```

### Step 5: Evaluate Results
```
WORKED → Document findings, proceed with confidence
FAILED → Document why, explore alternatives
PARTIAL → Identify what's missing, decide next step
```

## Example: YouTube Subtitles PoC

### Context
Building a system that: downloads YouTube subtitles → processes them → generates articles

### PoC Candidate Identification
```
UNKNOWNS:
1. Can we get subtitles from YouTube? (API? Scraping? Library?)
2. What format are subtitles in?
3. Are auto-generated subtitles good enough quality?

HIGH RISK:
- Everything depends on getting subtitles
- If this doesn't work, entire feature is blocked

POC DECISION: Test subtitle extraction FIRST
```

### PoC Implementation
```html
<!-- tools/tmp/poc/youtube-subtitles-poc.html -->
<!DOCTYPE html>
<html>
<head><title>PoC: YouTube Subtitles</title></head>
<body>
  <h1>PoC: Can we extract YouTube subtitles?</h1>

  <input id="url" placeholder="YouTube URL" style="width:400px">
  <button onclick="getSubtitles()">Get Subtitles</button>

  <h2>Result:</h2>
  <pre id="output" style="background:#f0f0f0;padding:10px;max-height:400px;overflow:auto"></pre>

  <script>
    async function getSubtitles() {
      const url = document.getElementById('url').value;
      const output = document.getElementById('output');

      output.textContent = 'Fetching...';

      try {
        // Try approach: use a subtitle API/service
        const videoId = extractVideoId(url);
        const subtitles = await fetchSubtitles(videoId);

        output.textContent = JSON.stringify(subtitles, null, 2);
      } catch (e) {
        output.textContent = 'FAILED: ' + e.message + '\n\nMay need different approach.';
      }
    }

    function extractVideoId(url) {
      // Minimal extraction - just prove concept
      const match = url.match(/v=([^&]+)/);
      return match ? match[1] : url;
    }

    async function fetchSubtitles(videoId) {
      // PoC: Try one approach
      // In real implementation, would have fallbacks
    }
  </script>
</body>
</html>
```

### Success Criteria
```
✅ SUCCESS if:
- We can input a YouTube URL
- We get back subtitle text
- Text is readable/usable quality

❌ FAILURE if:
- API requires paid access we can't afford
- CORS blocks browser access (may need server-side)
- Subtitles are gibberish/unusable
```

## PoC Decision Tree

```
Is this technically uncertain?
├─ NO → Skip PoC, just build it
└─ YES → Is it critical path?
         ├─ NO → Note risk, proceed, PoC if time
         └─ YES → PoC REQUIRED before full build
                  │
                  └─ What's the fastest way to test?
                     ├─ Browser works? → HTML PoC
                     ├─ Need server? → Script PoC
                     └─ Multi-system? → Integration PoC
```

## Anti-Patterns

| Don't | Do Instead |
|-------|------------|
| Build full feature as "PoC" | Minimal code, single concern |
| PoC in main codebase | Isolated in tools/tmp/poc/ |
| Spend days on PoC | Time-box to 1-2 hours |
| PoC well-known patterns | Just build those directly |
| Skip PoC for new APIs | Always PoC external dependencies |
| Perfect PoC code | Ugly code that answers the question |

## Documenting PoC Results

After completing a PoC, briefly document:

```markdown
## PoC: [Feature Name]
Date: YYYY-MM-DD
Result: SUCCESS / PARTIAL / FAILED

### What We Tested
[One sentence]

### What We Learned
- [Key finding 1]
- [Key finding 2]

### Implications for Full Build
- [How this affects our approach]

### Code Location
tools/tmp/poc/[filename] (or deleted if not needed)
```

## Quick Reference

```
/poc                    → Analyze spec, propose PoC candidates
/poc [specific thing]   → Create PoC for specific component
/poc check              → Review PoC results, decide next steps
```
