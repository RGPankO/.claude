---
argument-hint: [check | update | audit | clean | tree] (optional: specify dependency operation)
description: Manage dependencies - check for updates, security issues, unused packages, and visualize dependency tree
---

# Deps Command

## Purpose
Comprehensively manage project dependencies including checking for outdated packages, finding security vulnerabilities, identifying unused dependencies, updating packages safely, and understanding the dependency tree.

## Command Syntax
- `/deps` - Show dependency overview (outdated, vulnerable, unused)
- `/deps check` - Check for outdated packages
- `/deps update` - Update dependencies safely (minor/patch only)
- `/deps update major` - Update including major versions (with caution)
- `/deps audit` - Check for security vulnerabilities
- `/deps clean` - Remove unused dependencies
- `/deps tree` - Show dependency tree
- `/deps add [package]` - Add new dependency with version recommendation
- `/deps remove [package]` - Remove dependency and check for breaks
- `/deps why [package]` - Explain why a package is installed

## User Instructions Processing

**Arguments received:** `$ARGUMENTS`

Parse `$ARGUMENTS` for operations:
- Default/`check`: Show outdated packages
- `update`: Update safe dependencies
- `update major`: Update all including breaking changes
- `audit`: Security vulnerability scan
- `clean`: Remove unused packages
- `tree`: Visualize dependency tree
- `add <package>`: Add new package
- `remove <package>`: Remove package
- `why <package>`: Show why package is needed

## Command Instructions

### 1. Detect Package Manager and Project Type

```bash
# Detect package manager for JavaScript/Node.js
if [ -f "package-lock.json" ]; then
  PKG_MANAGER="npm"
elif [ -f "yarn.lock" ]; then
  PKG_MANAGER="yarn"
elif [ -f "pnpm-lock.yaml" ]; then
  PKG_MANAGER="pnpm"
elif [ -f "bun.lockb" ]; then
  PKG_MANAGER="bun"
fi

# Detect Python package manager
if [ -f "requirements.txt" ]; then
  PY_PKG_MANAGER="pip"
elif [ -f "Pipfile" ]; then
  PY_PKG_MANAGER="pipenv"
elif [ -f "poetry.lock" ]; then
  PY_PKG_MANAGER="poetry"
elif [ -f "pyproject.toml" ]; then
  # Could be poetry or pip with pyproject.toml
  grep -q "poetry" pyproject.toml && PY_PKG_MANAGER="poetry" || PY_PKG_MANAGER="pip"
fi

# Other package managers
[ -f "Cargo.toml" ] && RUST_PKG_MANAGER="cargo"
[ -f "go.mod" ] && GO_PKG_MANAGER="go"
[ -f "pom.xml" ] && JAVA_PKG_MANAGER="maven"
[ -f "build.gradle" ] && JAVA_PKG_MANAGER="gradle"
[ -f "Gemfile" ] && RUBY_PKG_MANAGER="bundler"
```

### 2. JavaScript/Node.js Dependency Management

```bash
# NPM operations
if [ "$PKG_MANAGER" = "npm" ]; then
  case "$ARGUMENTS" in
    "" | "check")
      echo "📦 Checking npm dependencies..."
      echo "
=== OUTDATED PACKAGES ==="
      npm outdated || echo "✅ All packages up to date"

      echo "
=== SECURITY AUDIT ==="
      npm audit --audit-level=moderate

      echo "
=== UNUSED DEPENDENCIES ==="
      npx depcheck
      ;;

    "update")
      echo "⬆️  Updating npm packages (minor/patch only)..."
      # Update package.json with latest minor/patch versions
      npx npm-check-updates -u --target minor
      npm install
      npm audit fix
      ;;

    "update major")
      echo "⚠️  Updating all packages including major versions..."
      npx npm-check-updates -u
      npm install
      npm audit fix --force
      echo "⚠️  Major updates applied - test thoroughly!"
      ;;

    "audit")
      echo "🔒 Running security audit..."
      npm audit
      echo "
💡 To fix automatically, run: npm audit fix
💡 For breaking changes, run: npm audit fix --force"
      ;;

    "clean")
      echo "🧹 Finding unused dependencies..."
      npx depcheck --json > /tmp/depcheck.json
      unused=$(npx depcheck | grep "Unused dependencies" -A 20 | tail -n +2)
      if [ -n "$unused" ]; then
        echo "Found unused dependencies:"
        echo "$unused"
        echo "
Remove with: npm uninstall $unused"
      else
        echo "✅ No unused dependencies found"
      fi
      ;;

    "tree")
      echo "🌳 Dependency tree:"
      npm list --depth=2
      ;;

    add*)
      package="${ARGUMENTS#add }"
      echo "➕ Adding $package..."
      npm view "$package" versions --json | tail -5
      npm install "$package"
      ;;

    remove*)
      package="${ARGUMENTS#remove }"
      echo "➖ Removing $package..."
      npm uninstall "$package"
      echo "Checking for broken dependencies..."
      npm list
      ;;

    why*)
      package="${ARGUMENTS#why }"
      echo "❓ Why is $package installed?"
      npm explain "$package"
      ;;
  esac
fi

# Yarn operations
if [ "$PKG_MANAGER" = "yarn" ]; then
  case "$ARGUMENTS" in
    "" | "check")
      echo "📦 Checking yarn dependencies..."
      yarn outdated
      yarn audit
      yarn run --silent depcheck 2>/dev/null || npx depcheck
      ;;

    "update")
      echo "⬆️  Updating yarn packages..."
      yarn upgrade-interactive --latest
      ;;

    "audit")
      echo "🔒 Running security audit..."
      yarn audit
      ;;

    "clean")
      echo "🧹 Cleaning yarn cache and finding unused..."
      yarn cache clean
      npx depcheck
      ;;

    "tree")
      echo "🌳 Dependency tree:"
      yarn list --depth=2
      ;;

    why*)
      package="${ARGUMENTS#why }"
      echo "❓ Why is $package installed?"
      yarn why "$package"
      ;;
  esac
fi

# PNPM operations
if [ "$PKG_MANAGER" = "pnpm" ]; then
  case "$ARGUMENTS" in
    "" | "check")
      echo "📦 Checking pnpm dependencies..."
      pnpm outdated
      pnpm audit
      ;;

    "update")
      echo "⬆️  Updating pnpm packages..."
      pnpm update --latest
      ;;

    "audit")
      echo "🔒 Running security audit..."
      pnpm audit
      ;;

    "tree")
      echo "🌳 Dependency tree:"
      pnpm list --depth=2
      ;;

    why*)
      package="${ARGUMENTS#why }"
      echo "❓ Why is $package installed?"
      pnpm why "$package"
      ;;
  esac
fi
```

### 3. Python Dependency Management

```bash
# Pip operations
if [ "$PY_PKG_MANAGER" = "pip" ]; then
  case "$ARGUMENTS" in
    "" | "check")
      echo "🐍 Checking Python dependencies..."
      pip list --outdated
      pip-audit 2>/dev/null || safety check 2>/dev/null || echo "Install pip-audit or safety for security scanning"
      ;;

    "update")
      echo "⬆️  Updating Python packages..."
      pip list --outdated --format=json | python -c "import sys, json; print('\n'.join([p['name'] for p in json.load(sys.stdin)]))" | xargs -n1 pip install -U
      ;;

    "audit")
      echo "🔒 Running security audit..."
      pip-audit || safety check || echo "Install pip-audit or safety: pip install pip-audit"
      ;;

    "clean")
      echo "🧹 Finding unused Python dependencies..."
      pip-autoremove --list 2>/dev/null || echo "Install pip-autoremove: pip install pip-autoremove"
      ;;

    "tree")
      echo "🌳 Dependency tree:"
      pipdeptree 2>/dev/null || echo "Install pipdeptree: pip install pipdeptree"
      ;;
  esac
fi

# Poetry operations
if [ "$PY_PKG_MANAGER" = "poetry" ]; then
  case "$ARGUMENTS" in
    "" | "check")
      echo "🐍 Checking Poetry dependencies..."
      poetry show --outdated
      poetry check
      ;;

    "update")
      echo "⬆️  Updating Poetry packages..."
      poetry update
      ;;

    "audit")
      echo "🔒 Running security audit..."
      poetry run safety check
      ;;

    "tree")
      echo "🌳 Dependency tree:"
      poetry show --tree
      ;;
  esac
fi

# Pipenv operations
if [ "$PY_PKG_MANAGER" = "pipenv" ]; then
  case "$ARGUMENTS" in
    "" | "check")
      echo "🐍 Checking Pipenv dependencies..."
      pipenv update --outdated
      pipenv check
      ;;

    "update")
      echo "⬆️  Updating Pipenv packages..."
      pipenv update
      ;;

    "audit")
      echo "🔒 Running security audit..."
      pipenv check
      ;;

    "tree")
      echo "🌳 Dependency tree:"
      pipenv graph
      ;;
  esac
fi
```

### 4. Other Languages

```bash
# Rust/Cargo
if [ "$RUST_PKG_MANAGER" = "cargo" ]; then
  case "$ARGUMENTS" in
    "" | "check")
      echo "🦀 Checking Rust dependencies..."
      cargo outdated
      cargo audit
      ;;

    "update")
      echo "⬆️  Updating Rust packages..."
      cargo update
      ;;

    "audit")
      echo "🔒 Running security audit..."
      cargo audit
      ;;

    "tree")
      echo "🌳 Dependency tree:"
      cargo tree
      ;;
  esac
fi

# Go
if [ "$GO_PKG_MANAGER" = "go" ]; then
  case "$ARGUMENTS" in
    "" | "check")
      echo "🐹 Checking Go dependencies..."
      go list -u -m all
      gosec ./... 2>/dev/null || echo "Install gosec for security scanning"
      ;;

    "update")
      echo "⬆️  Updating Go packages..."
      go get -u ./...
      go mod tidy
      ;;

    "audit")
      echo "🔒 Running security audit..."
      govulncheck ./... 2>/dev/null || echo "Install govulncheck: go install golang.org/x/vuln/cmd/govulncheck@latest"
      ;;

    "tree")
      echo "🌳 Dependency tree:"
      go mod graph
      ;;
  esac
fi

# Ruby/Bundler
if [ "$RUBY_PKG_MANAGER" = "bundler" ]; then
  case "$ARGUMENTS" in
    "" | "check")
      echo "💎 Checking Ruby dependencies..."
      bundle outdated
      bundle audit check 2>/dev/null || echo "Install bundler-audit: gem install bundler-audit"
      ;;

    "update")
      echo "⬆️  Updating Ruby packages..."
      bundle update
      ;;

    "audit")
      echo "🔒 Running security audit..."
      bundle audit check || gem install bundler-audit && bundle audit check
      ;;
  esac
fi
```

### 5. License and Size Analysis

```bash
# Check license compatibility
check_licenses() {
  echo "📜 Checking licenses..."

  if [ "$PKG_MANAGER" ]; then
    npx license-checker --summary 2>/dev/null || echo "Run: npx license-checker"
  fi

  if [ "$PY_PKG_MANAGER" ]; then
    pip-licenses 2>/dev/null || echo "Install pip-licenses: pip install pip-licenses"
  fi
}

# Analyze package sizes (for JavaScript)
check_sizes() {
  if [ "$PKG_MANAGER" ]; then
    echo "📊 Package sizes:"
    npx howfat 2>/dev/null || npx package-size 2>/dev/null || echo "Run: npx howfat"

    echo "
📦 Bundle size impact:"
    npx size-limit 2>/dev/null || echo "Configure size-limit for bundle analysis"
  fi
}
```

## Dependency Report Format

```
DEPENDENCY REPORT
=================

📊 Summary:
  Total Dependencies: 142
  Direct: 23
  Transitive: 119

⬆️  Outdated Packages (7):
  Package         Current  Wanted  Latest  Type
  express         4.17.1   4.17.3  5.0.0   major
  lodash          4.17.20  4.17.21 4.17.21 patch
  react           17.0.2   17.0.2  18.2.0  major
  webpack         5.70.0   5.76.0  5.88.0  minor

🔒 Security Issues (2):
  High     lodash@4.17.20     CVE-2021-xxxxx
  Moderate axios@0.21.1       CVE-2021-yyyyy

🧹 Unused Dependencies (3):
  - moment (consider: date-fns or dayjs)
  - jquery (not imported anywhere)
  - bootstrap (CSS not used)

📜 License Summary:
  MIT: 89%
  Apache-2.0: 8%
  ISC: 3%

📦 Largest Packages:
  1. node_modules/webpack: 12.3 MB
  2. node_modules/react-scripts: 8.7 MB
  3. node_modules/typescript: 6.2 MB

💡 Recommendations:
  - Update lodash to fix security vulnerability
  - Consider removing unused dependencies
  - Review major updates for breaking changes
  - Run '/deps audit' for detailed security report
```

## Advanced Operations

```bash
# Duplicate dependency detection
find_duplicates() {
  if [ "$PKG_MANAGER" = "npm" ]; then
    echo "🔍 Finding duplicate dependencies..."
    npm dedupe --dry-run
  fi
}

# Check peer dependency issues
check_peer_deps() {
  if [ "$PKG_MANAGER" = "npm" ]; then
    echo "👥 Checking peer dependencies..."
    npm list --depth=0 2>&1 | grep "peer dep" || echo "✅ No peer dependency issues"
  fi
}

# Analyze dependency updates impact
analyze_updates() {
  echo "🔬 Analyzing update impact..."

  # Check changelog for major updates
  if [ "$PKG_MANAGER" ]; then
    npx npm-check-updates -u --dry-run --format json > /tmp/updates.json
    echo "Major updates that might break:"
    cat /tmp/updates.json | jq '.[] | select(.from | test("^[0-9]+")) | select((.to | split(".")[0]) != (.from | split(".")[0]))'
  fi
}
```

## Error Handling

```bash
# Handle missing package manager
if [ -z "$PKG_MANAGER" ] && [ -z "$PY_PKG_MANAGER" ] && [ -z "$RUST_PKG_MANAGER" ] && [ -z "$GO_PKG_MANAGER" ]; then
  echo "❌ No package manager detected"
  echo "💡 Initialize a project first:"
  echo "  npm init          # for JavaScript"
  echo "  poetry init       # for Python"
  echo "  cargo init        # for Rust"
  echo "  go mod init       # for Go"
  exit 1
fi

# Handle network issues
handle_network_error() {
  echo "❌ Network error while checking dependencies"
  echo "💡 Check your internet connection or try again later"
}

# Handle permission issues
handle_permission_error() {
  echo "❌ Permission denied"
  echo "💡 You might need to run with elevated permissions or check file ownership"
}
```

## Usage Examples

**User says:** "/deps"
**Result:** Shows comprehensive dependency overview

**User says:** "/deps check"
**Result:** Checks for outdated packages

**User says:** "/deps update"
**Result:** Updates packages safely (minor/patch versions)

**User says:** "/deps audit"
**Result:** Runs security vulnerability scan

**User says:** "/deps clean"
**Result:** Identifies and offers to remove unused dependencies

**User says:** "/deps why lodash"
**Result:** Explains why lodash is in the project

---

This command provides comprehensive dependency management, helping maintain secure, up-to-date, and lean projects.