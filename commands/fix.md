---
argument-hint: [all | lint | format | imports | types] (optional: specify what to fix)
description: Auto-fix common code issues including linting, formatting, imports, and type errors
---

# Fix Command

## Purpose
Automatically fix common code issues including linting errors, formatting problems, import ordering, unused imports, and simple type errors across the entire project or specific areas.

## Command Syntax
- `/fix` - Run all available fixes
- `/fix all` - Same as /fix, run all fixes
- `/fix lint` - Fix only linting issues
- `/fix format` - Fix only formatting issues
- `/fix imports` - Fix import ordering and remove unused imports
- `/fix types` - Attempt to fix simple type errors
- `/fix [file/directory]` - Fix specific file or directory

## User Instructions Processing

**Arguments received:** `$ARGUMENTS`

Parse `$ARGUMENTS` for fix targets:
- No args or `all`: Run all fix operations
- `lint`: Run linters with --fix
- `format`: Run formatters
- `imports`: Fix import issues
- `types`: Fix type errors
- File/directory path: Target specific location

## Command Instructions

### 1. Detect Project Type and Tools

```bash
# Detect JavaScript/TypeScript project
if [ -f "package.json" ]; then
  PROJECT_TYPE="node"

  # Check for specific tools
  [ -f ".eslintrc.json" ] || [ -f ".eslintrc.js" ] || [ -f ".eslintrc.yml" ] && ESLINT=true
  [ -f ".prettierrc" ] || [ -f ".prettierrc.json" ] || [ -f ".prettierrc.js" ] && PRETTIER=true
  [ -f "tsconfig.json" ] && TYPESCRIPT=true
  grep -q "\"lint\"" package.json && HAS_LINT_SCRIPT=true
  grep -q "\"format\"" package.json && HAS_FORMAT_SCRIPT=true
fi

# Detect Python project
if [ -f "requirements.txt" ] || [ -f "setup.py" ] || [ -f "pyproject.toml" ]; then
  PROJECT_TYPE="python"

  # Check for specific tools
  [ -f ".flake8" ] || [ -f "setup.cfg" ] && FLAKE8=true
  [ -f ".black" ] || [ -f "pyproject.toml" ] && BLACK=true
  [ -f ".isort.cfg" ] || [ -f "setup.cfg" ] && ISORT=true
  [ -f "mypy.ini" ] || [ -f "setup.cfg" ] && MYPY=true
  command -v ruff >/dev/null 2>&1 && RUFF=true
fi

# Detect other project types
[ -f "Cargo.toml" ] && PROJECT_TYPE="rust"
[ -f "go.mod" ] && PROJECT_TYPE="go"
[ -f "pom.xml" ] || [ -f "build.gradle" ] && PROJECT_TYPE="java"
```

### 2. JavaScript/TypeScript Fixes

```bash
# Run ESLint fix
if [ "$ESLINT" = true ]; then
  echo "🔧 Running ESLint fixes..."
  npx eslint . --ext .js,.jsx,.ts,.tsx --fix --ignore-path .gitignore
fi

# Run Prettier format
if [ "$PRETTIER" = true ]; then
  echo "🎨 Running Prettier formatting..."
  npx prettier --write "**/*.{js,jsx,ts,tsx,json,css,scss,md}" --ignore-path .gitignore
fi

# Fix TypeScript errors (simple ones)
if [ "$TYPESCRIPT" = true ]; then
  echo "📘 Checking TypeScript errors..."
  npx tsc --noEmit --pretty

  # Auto-fix what's possible
  npx ts-migrate reignore . 2>/dev/null || echo "Complex type fixes need manual intervention"
fi

# Fix import ordering (if using eslint-plugin-import)
if grep -q "eslint-plugin-import" package.json 2>/dev/null; then
  echo "📦 Fixing import ordering..."
  npx eslint . --fix --rule 'import/order: error'
fi

# Remove unused imports (if using eslint-plugin-unused-imports)
if grep -q "eslint-plugin-unused-imports" package.json 2>/dev/null; then
  echo "🧹 Removing unused imports..."
  npx eslint . --fix --rule 'unused-imports/no-unused-imports: error'
fi

# Use project's npm scripts if available
if [ "$HAS_LINT_SCRIPT" = true ]; then
  echo "📋 Running project lint:fix script..."
  npm run lint:fix 2>/dev/null || npm run lint -- --fix 2>/dev/null
fi

if [ "$HAS_FORMAT_SCRIPT" = true ]; then
  echo "✨ Running project format script..."
  npm run format 2>/dev/null
fi
```

### 3. Python Fixes

```bash
# Run Black formatter
if [ "$BLACK" = true ] || command -v black >/dev/null 2>&1; then
  echo "⚫ Running Black formatter..."
  black . --exclude="venv/|.venv/|env/|build/|dist/"
fi

# Run isort for import ordering
if [ "$ISORT" = true ] || command -v isort >/dev/null 2>&1; then
  echo "📦 Fixing import ordering with isort..."
  isort . --skip venv --skip .venv --skip env
fi

# Run Ruff (fast Python linter with fixes)
if [ "$RUFF" = true ]; then
  echo "🦀 Running Ruff fixes..."
  ruff check . --fix
  ruff format .
fi

# Run autopep8 if available
if command -v autopep8 >/dev/null 2>&1; then
  echo "🔧 Running autopep8 fixes..."
  autopep8 --in-place --recursive --aggressive .
fi

# Run flake8 (note: flake8 doesn't auto-fix, just reports)
if [ "$FLAKE8" = true ]; then
  echo "📊 Checking flake8 issues (manual fixes needed)..."
  flake8 . --exclude=venv,.venv,env,build,dist --count --statistics
fi

# Fix type hints with pyupgrade
if command -v pyupgrade >/dev/null 2>&1; then
  echo "⬆️  Upgrading Python syntax..."
  find . -name "*.py" -not -path "*/venv/*" -not -path "*/.venv/*" | xargs pyupgrade --py36-plus
fi
```

### 4. General Fixes (All Languages)

```bash
# Remove trailing whitespace
echo "🧹 Removing trailing whitespace..."
find . -type f \( -name "*.js" -o -name "*.ts" -o -name "*.jsx" -o -name "*.tsx" -o -name "*.py" -o -name "*.go" -o -name "*.rs" -o -name "*.java" -o -name "*.css" -o -name "*.scss" \) -not -path "*/node_modules/*" -not -path "*/.git/*" -not -path "*/venv/*" -exec sed -i '' 's/[[:space:]]*$//' {} \;

# Fix line endings (UNIX style)
echo "📐 Fixing line endings..."
find . -type f \( -name "*.js" -o -name "*.ts" -o -name "*.jsx" -o -name "*.tsx" -o -name "*.py" -o -name "*.go" -o -name "*.rs" -o -name "*.java" \) -not -path "*/node_modules/*" -not -path "*/.git/*" -not -path "*/venv/*" -exec dos2unix {} \; 2>/dev/null

# Ensure files end with newline
echo "📄 Ensuring files end with newline..."
find . -type f \( -name "*.js" -o -name "*.ts" -o -name "*.jsx" -o -name "*.tsx" -o -name "*.py" -o -name "*.go" -o -name "*.rs" -o -name "*.java" \) -not -path "*/node_modules/*" -not -path "*/.git/*" -not -path "*/venv/*" -exec sh -c 'tail -c1 {} | read -r _ || echo >> {}' \;

# Remove console.log statements (with confirmation)
echo "🔍 Checking for console.log statements..."
grep -r "console.log" --include="*.js" --include="*.ts" --include="*.jsx" --include="*.tsx" --exclude-dir=node_modules --exclude-dir=.git . | head -10
```

### 5. Language-Specific Fixes

```bash
# Rust fixes
if [ "$PROJECT_TYPE" = "rust" ]; then
  echo "🦀 Running Rust fixes..."
  cargo fmt
  cargo clippy --fix --allow-dirty --allow-staged
fi

# Go fixes
if [ "$PROJECT_TYPE" = "go" ]; then
  echo "🐹 Running Go fixes..."
  go fmt ./...
  goimports -w .
  golangci-lint run --fix
fi

# Java fixes (if spotless is configured)
if [ "$PROJECT_TYPE" = "java" ]; then
  echo "☕ Running Java fixes..."
  [ -f "pom.xml" ] && mvn spotless:apply
  [ -f "build.gradle" ] && ./gradlew spotlessApply
fi
```

## Fix Report Format

```
FIX REPORT
==========

✅ Successfully Fixed:
  - ESLint: 23 problems fixed
  - Prettier: 15 files formatted
  - Imports: 8 unused imports removed
  - Whitespace: 12 files cleaned

⚠️  Manual Fixes Needed:
  - TypeScript: 3 type errors need manual resolution
  - ESLint: 2 problems require manual fixes

📊 Summary:
  Files processed: 45
  Auto-fixed: 38
  Manual required: 5

🔍 Remaining Issues:
  Run '/status' to see current state
```

## Error Handling

```bash
# Catch and report errors gracefully
handle_error() {
  echo "❌ Error during $1:"
  echo "  $2"
  echo "  Try running the specific tool manually for more details"
}

# Example usage in commands
npx eslint . --fix 2>&1 || handle_error "ESLint" "$?"
```

## Performance Optimization

- Run fixes in parallel when possible
- Cache detection results for repeated runs
- Skip directories like node_modules, .git, venv
- Provide progress indicators for long operations
- Allow targeting specific files/directories

## Safety Measures

- Create backup before major fixes (optional)
- Show preview of changes for destructive operations
- Allow dry-run mode to see what would be fixed
- Respect .gitignore patterns
- Never modify git history or .git directory

## Usage Examples

**User says:** "/fix"
**Result:** Runs all available auto-fixes for detected project type

**User says:** "/fix lint"
**Result:** Runs only linting fixes (ESLint, flake8, etc.)

**User says:** "/fix format"
**Result:** Runs only formatting tools (Prettier, Black, etc.)

**User says:** "/fix imports"
**Result:** Fixes import ordering and removes unused imports

**User says:** "/fix src/"
**Result:** Runs all fixes only on the src/ directory

---

This command saves significant time by automating common code quality fixes that developers would otherwise do manually.