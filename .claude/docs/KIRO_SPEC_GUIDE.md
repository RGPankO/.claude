# Kiro Spec Writing Guide (Dummy-Proof Reference)

Use this guide to draft specs Kiro will accept without rewrites. It covers format, wording, examples, and the “visible increment” principle (each section should produce something testable in the UI unless it is API-only).

---

## Files and Layout
- One feature per folder under `.kiro/specs/{feature}/`.
- Three files:
  - `requirements.md`
  - `design.md`
  - `tasks.md`

---

## requirements.md

### Sections (order)
1) **Title:** `# Requirements Document`
2) **Introduction:** 1–2 sentences stating the feature scope.
3) **Glossary:** Bullet definitions of all technical terms.
4) **Requirements:** Each as `### Requirement N: Title`
   - **User Story:** `As a [role], I want [feature], so that [benefit].`
   - **Acceptance Criteria:** `#### Acceptance Criteria` followed by numbered EARS-style items.

### EARS Style (strict)
- Patterns: `WHEN ... THE system SHALL ...`, `GIVEN ... WHEN ... THE system SHALL ...`, `IF ... THEN ...` (prefer WHEN/GIVEN).
- Positive phrasing only (no “SHALL NOT”).
- No vague terms (“safe”, “gracefully”, “deterministic enough”). Be measurable and specific.

### Example Requirement
```
### Requirement 3: Scripted Timeline Simulation

User Story: As a tester, I want to define custom price scenarios with waypoints, so that I can see how the bot reacts to predictable moves.

#### Acceptance Criteria
1. GIVEN a list of waypoints with timestamp and price, WHEN the timeline runs, THE system SHALL interpolate prices linearly between consecutive waypoints.
2. WHEN the timeline reaches the final waypoint and loop mode is enabled, THE system SHALL restart from the first waypoint.
3. WHEN the timeline reaches the final waypoint and loop mode is disabled, THE system SHALL stop emitting price updates.
4. WHEN timestamps are relative, THE system SHALL treat them as seconds from simulation start.
5. WHEN timestamps are absolute, THE system SHALL treat them as datetimes.
6. WHEN emitting interpolated prices, THE system SHALL respect the configured cadence between ticks.
```

### What NOT to put in requirements.md
- No testing requirements (move to design.md).
- No negatives (“SHALL NOT”).
- No vague words; always specify exact behavior/limits.
- No mixed headings; keep consistent numbering and headers.

---

## design.md

### Sections (order)
1) **Title:** `# Design Document: {Feature}`
2) **Overview:** What this design covers.
3) **Architecture:** Dedicated `## Architecture` section; diagram allowed (ASCII is fine).
4) **Components and Interfaces:** With TypeScript-like interfaces where helpful.
5) **Data Models:** Interfaces or schema snippets for new/changed data.
6) **Correctness Properties:** Dedicated section; see below.
7) **Error Handling:** Table or bullets of error types and strategies.
8) **Testing Strategy:** Dual approach (unit + property), library and iteration counts, sample test snippet.

### Correctness Properties (must include)
- Format: Universally quantified “For any …” statements.
- Each property must reference requirements: `**Validates: Requirements X.Y**`
- Example:
```
Property 4: VWAP Calculation Correctness
For any sequence of partial fills with prices P1..Pn and quantities Q1..Qn, the VWAP SHALL equal sum(Pi*Qi)/sum(Qi).
Validates: Requirements 5.5
```

### Testing Strategy (must include)
- State the property-based testing library (e.g., fast-check).
- Minimum iterations: 100 for property tests.
- Mention dual testing (unit + property) and where to put tests (`*.test.ts`, `*.property.test.ts`).
- Provide a short example test snippet with the property reference in a comment.

### Example Architecture Snippet
```
## Architecture
Connector Factory -> MockExchangeConnector (Price Generator, Order Book, Fill Engine, Fee Calc, WS Emitter)
                                   |
                              Price Generators (Random, Sine, Preset, Timeline, Replay)
```

---

## tasks.md

### Format
- Title: `# Implementation Plan`
- Use checkboxes with decimal numbering:
  - `- [ ] 1.1 Do thing`
  - Optional/test tasks: `- [ ]* 1.2 Write property test ...`
- Every task includes requirement references: `_Requirements: X.Y_`
- Add checkpoints after major groups: “Ensure all tests pass, ask if questions arise.”
- No “Epic” headers; just numbered groups.

### Property Test Tasks
- Keep close to the implementation they validate.
- Format:
```
- [ ]* 4.3 Write property test for timeline interpolation
  - **Property 1: Timeline Linear Interpolation**
  - **Validates: Requirements 3.1**
```

### Example Task Block
```
- [ ] 2. Price Generator Framework
  - [ ] 2.1 Create PriceGenerator interface
    - start/stop, onTick callback, getCurrentPrice
    - _Requirements: 2.1_
  - [ ] 2.2 Implement Random Walk generator
    - Drift, volatility, clamps, cadence
    - _Requirements: 2.2_
  - [ ]* 2.3 Write unit tests for generators
    - Bounds, shape, preset behavior
    - _Requirements: 2.2, 2.3, 2.4_
```

---

## Visible Increment Principle
- For any section that is not strictly backend/API-only, end with a user-visible, testable increment in the UI.
- Provide brief testing instructions (align with `.claude/docs/TESTING_REQUEST_GUIDE.md`):
  - Where to navigate/click.
  - What to expect (states, metrics, events).
  - Any mock data/timelines to use.
- Purpose: ensure continuous visual progress and easy validation after each section.

Example in a requirement or task note:
```
After implementing timeline playback, ensure there is a demo deployment with a sample 50k → 150k → 50k timeline; testers can start it, open the dashboard, and see price/WS updates without refresh.
```

---

## Checklist Before Sharing with Kiro
- Requirements: User stories present; EARS acceptance criteria; no negatives/vague terms; testing removed.
- Design: Architecture section; components/interfaces; data models; correctness properties with requirement refs; error handling; testing strategy (fast-check, 100 iterations, dual approach).
- Tasks: Numbered checkboxes with requirement refs; property test tasks marked; checkpoints added; optional tasks marked with `*`.
- Visible increment noted where applicable (UI/test instructions).

Following this guide should prevent Kiro rewrites and keep specs aligned with project expectations. *** End Patch
