# Kiro Testing Guide

> **Context Loading**: Reference from `/kiro` command and `test-generator` agent. Load if not already in session.

This guide documents the testing approach for Kiro task execution. Follow this when implementing and testing tasks.

## My Testing Philosophy

I don't do strict TDD (write tests first, then code). Instead, I do **implementation-first development**:

1. Implement the feature/function
2. Write tests to verify it works
3. Run tests to confirm
4. Fix any issues found

Why? Because I need to understand the shape of the code before I can write meaningful tests. The spec tells me WHAT to build, but the implementation reveals HOW it works.

## My Workflow for Each Task

### Step 1: Read the Requirements

Before writing any code, I read:
- The task description in `tasks.md`
- The linked requirements in `requirements.md`
- The relevant section in `design.md`

I look for:
- What inputs does this function take?
- What outputs should it produce?
- What are the edge cases mentioned?
- Is there a correctness property I need to test?

### Step 2: Create the Types First

I always start with TypeScript interfaces. This forces me to think about the data shape.

```typescript
// src/services/volatility/types.ts
export interface OHLCV {
  timestamp: number;
  open: number;
  high: number;
  low: number;
  close: number;
  volume: number;
}

export interface ATRResult {
  atr: number;
  atrPercent: number;
  periodCount: number;
}
```

### Step 3: Implement the Function

Write the actual code. Keep it simple and focused.

```typescript
// src/services/volatility/atr.ts
export function calculateATR(candles: OHLCV[], currentPrice: number): ATRResult {
  // Implementation here...
}
```

### Step 4: Write Tests

Now I write tests. I put the test file RIGHT NEXT to the source file:
- `atr.ts` → `atr.test.ts`
- `volatility-calculator.ts` → `volatility-calculator.test.ts`

#### My Test Structure

```typescript
import { calculateATR } from './atr';
import { OHLCV } from './types';

describe('calculateATR', () => {
  // 1. Happy path - normal usage
  it('should calculate ATR for valid candles', () => {
    const candles: OHLCV[] = [/* test data */];
    const result = calculateATR(candles, 100);
    expect(result.atr).toBe(20);
  });

  // 2. Edge cases - empty, zero, boundaries
  it('should return zero for empty candles array', () => {
    const result = calculateATR([], 100);
    expect(result.atr).toBe(0);
  });

  // 3. Error cases - invalid inputs
  it('should throw error for non-positive price', () => {
    expect(() => calculateATR([], 0)).toThrow('Current price must be positive');
  });
});
```

### Step 5: Run Tests

```bash
# Run just my new test file
npm test -- --testPathPattern=atr.test.ts

# If it fails, I check the error and fix either the code or the test
```

### Step 6: Check for TypeScript Errors

Before committing, I always check:
```bash
npx tsc --noEmit
```

Or I use `getDiagnostics` to check specific files.

## How I Handle Property-Based Tests

Property tests are special. They verify that a rule holds for ALL possible inputs, not just examples.

### When I Write Property Tests

I write property tests when the task says:
- `Write property test for X`
- The design doc has a "Property N: ..." that needs testing

### My Property Test Format

```typescript
import fc from 'fast-check';

describe('Volatility Calculator', () => {
  /**
   * **Feature: geometric-grid-trading-bot, Property 1: Volatility Index Bounds**
   * **Validates: Requirements 1.4**
   */
  it('should always produce volatility index in [0, 1]', () => {
    fc.assert(
      fc.property(
        // Generator: creates random valid inputs
        fc.float({ min: 0.01, max: 100000 }),  // currentPrice
        (currentPrice) => {
          const priceData = createTestPriceData();
          const result = calculateVolatilityIndex(priceData, currentPrice);
          
          // Property: must ALWAYS be true
          return result.volatilityIndex >= 0 && result.volatilityIndex <= 1;
        }
      ),
      { numRuns: 100 }  // Run 100 random tests
    );
  });
});
```

### The Comment Format is REQUIRED

I always include this exact comment format:
```typescript
/**
 * **Feature: geometric-grid-trading-bot, Property N: Property Name**
 * **Validates: Requirements X.Y**
 */
```

This links the test to the design document.

### When Property Tests Fail

This is critical. When a property test fails, I DON'T just fix it blindly.

I ask myself:
1. **Is the test wrong?** Did I generate invalid inputs?
2. **Is the code buggy?** Does the implementation have a real bug?
3. **Is the spec incomplete?** Does the requirement need clarification?

If I'm not sure, I ask the user before changing anything.

## How I Test Database Models

Database tests are different because they need a real database.

### My Database Test Pattern

```typescript
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

describe('Database Model Tests', () => {
  // Clean up BEFORE tests (in case previous run failed)
  beforeAll(async () => {
    await prisma.someModel.deleteMany({ where: { username: { startsWith: 'test_' } } });
  });

  // Clean up AFTER tests
  afterAll(async () => {
    await prisma.someModel.deleteMany({ where: { username: { startsWith: 'test_' } } });
    await prisma.$disconnect();
  });

  it('should enforce unique constraint', async () => {
    // Create first record
    const record1 = await prisma.user.create({
      data: { username: 'test_unique', passwordHash: 'hash' },
    });

    // Try to create duplicate - should fail
    await expect(
      prisma.user.create({
        data: { username: 'test_unique', passwordHash: 'hash2' },
      })
    ).rejects.toThrow();

    // Clean up this specific test
    await prisma.user.delete({ where: { id: record1.id } });
  });
});
```

### Key Points:
- Use `test_` prefix for test data so cleanup is easy
- Clean up in `beforeAll` AND `afterAll`
- Clean up each test's data at the end of the test
- Always `$disconnect()` in `afterAll`

## My Test Helpers

I create helper functions to reduce repetition:

```typescript
// Helper to create test OHLCV data
function createCandle(high: number, low: number, close: number): OHLCV {
  return {
    timestamp: Date.now(),
    open: (high + low) / 2,
    high,
    low,
    close,
    volume: 1000,
  };
}

// Helper to create price data for all timeframes
function createUniformPriceData(basePrice: number, volatilityPercent: number): PriceDataByTimeframe {
  const range = basePrice * (volatilityPercent / 100);
  const candle = createCandle(basePrice + range/2, basePrice - range/2, basePrice);
  
  const priceData: Partial<PriceDataByTimeframe> = {};
  for (const tf of ALL_TIMEFRAMES) {
    priceData[tf] = [candle, candle, candle];
  }
  return priceData as PriceDataByTimeframe;
}
```

## Commands I Use

```bash
# Run all tests
npm test

# Run specific test file
npm test -- --testPathPattern=atr.test.ts

# Run tests matching a name
npm test -- -t "should calculate ATR"

# Run with verbose output (see all test names)
npm test -- --verbose

# Run and show coverage
npm run test:coverage
```

## Common Issues I Fix

### 1. TypeScript "unused variable" error in tests
```
error TS6133: 'Timeframe' is declared but its value is never read.
```
**Fix**: Remove the unused import.

### 2. Test timeout
```
Timeout - Async callback was not invoked within 5000ms
```
**Fix**: Add `jest.setTimeout(30000)` or check for hanging promises.

### 3. Database connection error
```
Can't reach database server
```
**Fix**: Make sure PostgreSQL is running and `.env` has correct `DATABASE_URL`.

### 4. Prisma client outdated
```
PrismaClientInitializationError
```
**Fix**: Run `npx prisma generate`.

## What I Test vs What I Skip

### I ALWAYS Test:
- Core business logic (calculations, transformations)
- Database constraints (unique, required fields, enums)
- Error handling (invalid inputs should throw)
- Edge cases (empty arrays, zero values, boundaries)
- Properties from the design document

### I DON'T Test:
- Simple getters/setters
- Framework code (Express routes just call services)
- Third-party libraries
- UI styling

## Summary: My Testing Checklist

When implementing a task:

- [ ] Read requirements and design doc
- [ ] Create types/interfaces first
- [ ] Implement the function
- [ ] Create test file next to source file
- [ ] Write happy path test
- [ ] Write edge case tests (empty, zero, null)
- [ ] Write error case tests
- [ ] If task mentions property test, write it with the required comment format
- [ ] Run tests: `npm test -- --testPathPattern=myfile.test.ts`
- [ ] Fix any failures
- [ ] Check TypeScript: `npx tsc --noEmit` or use getDiagnostics
- [ ] Mark task complete

That's it. This is exactly how I test.
