---
argument-hint: [pattern | watch | coverage | failed | file.js] (optional: run specific tests or modes)
description: Smart test execution with pattern matching, watch mode, coverage, and failure retry
---

# Test Command

## Purpose
Intelligently run tests based on context - run all tests, specific test files, only changed files, previously failed tests, or tests in watch mode with coverage reporting.

## Command Syntax
- `/test` - Run all tests (or smart selection based on recent changes)
- `/test [pattern]` - Run tests matching pattern (e.g., "auth", "user.test")
- `/test watch` - Run tests in watch mode
- `/test coverage` - Run tests with coverage report
- `/test failed` - Re-run only previously failed tests
- `/test changed` - Run only tests for changed files
- `/test [file]` - Run specific test file
- `/test unit` - Run only unit tests
- `/test integration` - Run only integration tests
- `/test e2e` - Run only end-to-end tests

## User Instructions Processing

**Arguments received:** `$ARGUMENTS`

Parse `$ARGUMENTS` for test targets:
- No args: Run default test suite or smart selection
- Pattern matching: Use grep pattern to filter tests
- Special keywords: `watch`, `coverage`, `failed`, `changed`
- Test types: `unit`, `integration`, `e2e`
- File paths: Run specific test files

## Command Instructions

### 1. Detect Test Framework and Configuration

```bash
# Node.js/JavaScript projects
if [ -f "package.json" ]; then
  # Detect test framework
  if grep -q "\"jest\"" package.json; then
    TEST_FRAMEWORK="jest"
  elif grep -q "\"mocha\"" package.json; then
    TEST_FRAMEWORK="mocha"
  elif grep -q "\"vitest\"" package.json; then
    TEST_FRAMEWORK="vitest"
  elif grep -q "\"cypress\"" package.json; then
    HAS_CYPRESS=true
  elif grep -q "\"playwright\"" package.json; then
    HAS_PLAYWRIGHT=true
  fi

  # Check for test scripts
  grep -q "\"test\"" package.json && HAS_TEST_SCRIPT=true
  grep -q "\"test:watch\"" package.json && HAS_WATCH_SCRIPT=true
  grep -q "\"test:coverage\"" package.json && HAS_COVERAGE_SCRIPT=true
  grep -q "\"test:unit\"" package.json && HAS_UNIT_SCRIPT=true
  grep -q "\"test:integration\"" package.json && HAS_INTEGRATION_SCRIPT=true
  grep -q "\"test:e2e\"" package.json && HAS_E2E_SCRIPT=true
fi

# Python projects
if [ -f "pytest.ini" ] || [ -f "setup.cfg" ] || [ -f "pyproject.toml" ]; then
  TEST_FRAMEWORK="pytest"
elif [ -f "manage.py" ] && grep -q "test" manage.py; then
  TEST_FRAMEWORK="django"
fi

# Other languages
[ -f "Cargo.toml" ] && TEST_FRAMEWORK="cargo"
[ -f "go.mod" ] && TEST_FRAMEWORK="go"
[ -f "pom.xml" ] && TEST_FRAMEWORK="maven"
[ -f "build.gradle" ] && TEST_FRAMEWORK="gradle"
```

### 2. Smart Test Selection

```bash
# Get recently changed files for smart test selection
get_changed_files() {
  # Files changed but not committed
  git diff --name-only 2>/dev/null

  # Files changed in last commit
  git diff --name-only HEAD~1 2>/dev/null
}

# Find related test files for changed source files
find_related_tests() {
  changed_files=$(get_changed_files)
  for file in $changed_files; do
    # JavaScript/TypeScript patterns
    if [[ $file =~ \.(js|jsx|ts|tsx)$ ]]; then
      # Convert source file to test file patterns
      test_file="${file%.*}.test.*"
      spec_file="${file%.*}.spec.*"
      test_dir="$(dirname "$file")/__tests__/$(basename "$file")"

      # Check if test files exist
      [ -f "$test_file" ] && echo "$test_file"
      [ -f "$spec_file" ] && echo "$spec_file"
      [ -f "$test_dir" ] && echo "$test_dir"
    fi

    # Python patterns
    if [[ $file =~ \.py$ ]]; then
      test_file="test_$(basename "$file")"
      test_dir="tests/$(basename "$file")"

      [ -f "$test_file" ] && echo "$test_file"
      [ -f "$test_dir" ] && echo "$test_dir"
    fi
  done
}
```

### 3. JavaScript/TypeScript Test Execution

```bash
# Jest
if [ "$TEST_FRAMEWORK" = "jest" ]; then
  case "$ARGUMENTS" in
    "watch")
      echo "👁️  Running Jest in watch mode..."
      npx jest --watch
      ;;
    "coverage")
      echo "📊 Running Jest with coverage..."
      npx jest --coverage --colors
      ;;
    "failed")
      echo "🔴 Re-running failed tests..."
      npx jest --onlyFailures
      ;;
    "changed")
      echo "📝 Running tests for changed files..."
      npx jest --onlyChanged
      ;;
    "unit")
      echo "🧪 Running unit tests..."
      npx jest --testPathPattern="unit|spec" --testPathIgnorePatterns="integration|e2e"
      ;;
    "integration")
      echo "🔗 Running integration tests..."
      npx jest --testPathPattern="integration"
      ;;
    "e2e")
      echo "🌐 Running e2e tests..."
      npx jest --testPathPattern="e2e"
      ;;
    "")
      echo "✅ Running all tests..."
      npx jest --colors
      ;;
    *)
      echo "🔍 Running tests matching '$ARGUMENTS'..."
      npx jest --testNamePattern="$ARGUMENTS" --colors
      ;;
  esac
fi

# Vitest
if [ "$TEST_FRAMEWORK" = "vitest" ]; then
  case "$ARGUMENTS" in
    "watch")
      echo "👁️  Running Vitest in watch mode..."
      npx vitest watch
      ;;
    "coverage")
      echo "📊 Running Vitest with coverage..."
      npx vitest run --coverage
      ;;
    "changed")
      echo "📝 Running tests for changed files..."
      npx vitest run --changed
      ;;
    *)
      echo "✅ Running tests..."
      npx vitest run $ARGUMENTS
      ;;
  esac
fi

# Mocha
if [ "$TEST_FRAMEWORK" = "mocha" ]; then
  case "$ARGUMENTS" in
    "watch")
      echo "👁️  Running Mocha in watch mode..."
      npx mocha --watch
      ;;
    "coverage")
      echo "📊 Running Mocha with coverage..."
      npx nyc mocha
      ;;
    *)
      echo "✅ Running tests..."
      npx mocha $ARGUMENTS
      ;;
  esac
fi

# Use npm scripts if available
if [ "$HAS_TEST_SCRIPT" = true ]; then
  case "$ARGUMENTS" in
    "watch")
      [ "$HAS_WATCH_SCRIPT" = true ] && npm run test:watch || npm test -- --watch
      ;;
    "coverage")
      [ "$HAS_COVERAGE_SCRIPT" = true ] && npm run test:coverage || npm test -- --coverage
      ;;
    "unit")
      [ "$HAS_UNIT_SCRIPT" = true ] && npm run test:unit || npm test -- --testPathPattern="unit"
      ;;
    "integration")
      [ "$HAS_INTEGRATION_SCRIPT" = true ] && npm run test:integration || npm test
      ;;
    "e2e")
      [ "$HAS_E2E_SCRIPT" = true ] && npm run test:e2e || npm test
      ;;
    *)
      npm test $ARGUMENTS
      ;;
  esac
fi
```

### 4. Python Test Execution

```bash
# Pytest
if [ "$TEST_FRAMEWORK" = "pytest" ]; then
  case "$ARGUMENTS" in
    "watch")
      echo "👁️  Running pytest in watch mode..."
      pytest-watch
      ;;
    "coverage")
      echo "📊 Running pytest with coverage..."
      pytest --cov=. --cov-report=term-missing --cov-report=html
      ;;
    "failed")
      echo "🔴 Re-running failed tests..."
      pytest --lf
      ;;
    "changed")
      echo "📝 Running tests for changed files..."
      pytest --picked
      ;;
    "unit")
      echo "🧪 Running unit tests..."
      pytest tests/unit -v
      ;;
    "integration")
      echo "🔗 Running integration tests..."
      pytest tests/integration -v
      ;;
    "")
      echo "✅ Running all tests..."
      pytest -v --color=yes
      ;;
    *)
      echo "🔍 Running tests matching '$ARGUMENTS'..."
      pytest -k "$ARGUMENTS" -v --color=yes
      ;;
  esac
fi

# Django
if [ "$TEST_FRAMEWORK" = "django" ]; then
  case "$ARGUMENTS" in
    "coverage")
      echo "📊 Running Django tests with coverage..."
      coverage run --source='.' manage.py test
      coverage report
      ;;
    *)
      echo "✅ Running Django tests..."
      python manage.py test $ARGUMENTS
      ;;
  esac
fi
```

### 5. Other Languages

```bash
# Rust
if [ "$TEST_FRAMEWORK" = "cargo" ]; then
  case "$ARGUMENTS" in
    "watch")
      echo "👁️  Running cargo tests in watch mode..."
      cargo watch -x test
      ;;
    "coverage")
      echo "📊 Running tests with coverage..."
      cargo tarpaulin
      ;;
    *)
      echo "✅ Running cargo tests..."
      cargo test $ARGUMENTS
      ;;
  esac
fi

# Go
if [ "$TEST_FRAMEWORK" = "go" ]; then
  case "$ARGUMENTS" in
    "coverage")
      echo "📊 Running Go tests with coverage..."
      go test -cover ./...
      ;;
    "watch")
      echo "👁️  Running Go tests in watch mode..."
      gotestsum --watch
      ;;
    *)
      echo "✅ Running Go tests..."
      go test ./... $ARGUMENTS
      ;;
  esac
fi

# Java (Maven)
if [ "$TEST_FRAMEWORK" = "maven" ]; then
  echo "✅ Running Maven tests..."
  mvn test $ARGUMENTS
fi

# Java (Gradle)
if [ "$TEST_FRAMEWORK" = "gradle" ]; then
  echo "✅ Running Gradle tests..."
  ./gradlew test $ARGUMENTS
fi
```

### 6. Test Output Processing

```bash
# Parse and format test results
format_test_results() {
  # Capture test output
  output=$1

  # Extract key metrics
  if echo "$output" | grep -q "passed"; then
    passed=$(echo "$output" | grep -oE "[0-9]+ passed" | head -1)
    failed=$(echo "$output" | grep -oE "[0-9]+ failed" | head -1)
    skipped=$(echo "$output" | grep -oE "[0-9]+ skipped" | head -1)

    echo "
TEST RESULTS
============
✅ Passed: $passed
❌ Failed: $failed
⏭️  Skipped: $skipped
"
  fi

  # Show coverage if available
  if echo "$output" | grep -q "Coverage"; then
    coverage=$(echo "$output" | grep -oE "[0-9]+(\.[0-9]+)?%" | head -1)
    echo "📊 Coverage: $coverage"
  fi
}

# Save failed tests for retry
save_failed_tests() {
  echo "$1" > .test-failures
}

# Load previously failed tests
load_failed_tests() {
  [ -f .test-failures ] && cat .test-failures
}
```

## Test Report Format

```
TEST EXECUTION REPORT
====================

🧪 Test Framework: Jest
📁 Test Files: 23
⏱️  Duration: 4.2s

Results:
  ✅ Passed: 142/150
  ❌ Failed: 3
  ⏭️  Skipped: 5

Failed Tests:
  ❌ UserAuth › should handle invalid tokens
  ❌ API › should retry on network error
  ❌ Database › should rollback on failure

Coverage:
  📊 Overall: 78.4%
  📁 Statements: 456/582
  🌿 Branches: 123/157
  🔧 Functions: 89/102

💡 Tips:
  - Run '/test failed' to retry failed tests
  - Run '/test coverage' for detailed coverage report
  - Run '/test watch' for continuous testing
```

## Performance Optimization

- Run tests in parallel when possible
- Use test caching for unchanged files
- Skip setup/teardown for unchanged fixtures
- Prioritize recently changed/failed tests
- Use minimal reporter in CI environments

## Error Handling

```bash
# Handle missing test framework
if [ -z "$TEST_FRAMEWORK" ]; then
  echo "❌ No test framework detected"
  echo "💡 Try installing a test framework:"
  echo "  npm install --save-dev jest    # for JavaScript"
  echo "  pip install pytest             # for Python"
  exit 1
fi

# Handle test failures gracefully
run_tests() {
  # Run tests and capture exit code
  $1
  exit_code=$?

  if [ $exit_code -ne 0 ]; then
    echo "
❌ Tests failed with exit code $exit_code
💡 Run '/test failed' to retry failed tests
📋 Run '/fix' to auto-fix common issues
"
  fi

  return $exit_code
}
```

## Usage Examples

**User says:** "/test"
**Result:** Runs all tests or smart selection based on changes

**User says:** "/test auth"
**Result:** Runs tests matching "auth" pattern

**User says:** "/test watch"
**Result:** Starts test runner in watch mode

**User says:** "/test coverage"
**Result:** Runs tests with coverage report

**User says:** "/test failed"
**Result:** Re-runs only previously failed tests

**User says:** "/test src/user.test.js"
**Result:** Runs specific test file

---

This command streamlines test execution with intelligent defaults and powerful options for different testing scenarios.