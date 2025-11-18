# File Organization

## Project Structure

- `client/js/` - All client-side JavaScript modules
- `client/js/core/` - Manager classes with dependency injection pattern
- `client/levels/` - Level data in JSON format
- `server/` - Node.js server code
- `tests/unit/` - Jest unit tests
- `tests/e2e/` - Playwright browser tests
- Entry points: `client/js/main.js` (client), `server/index.js` (server)

## Current Directory Structure

```
client/js/
├── core/           # Core game systems (managers with DI pattern)
│   ├── render-manager.js      # Rendering coordination
│   ├── input-manager.js       # Input handling coordination
│   ├── game-coordinator.js    # State transitions and level loading
│   └── entity-manager.js      # Entity lifecycle management
├── entities/       # Game entities (Tank, Projectile, Base, Terrain)
├── ai/            # AI system (controllers, behaviors)
├── rendering/     # Rendering and UI systems
├── input/         # Input handling
├── levels/        # Level management
├── editor/        # Level editor
└── utils/         # Utilities and helpers

tests/
├── unit/          # Jest unit tests
├── integration/   # Integration tests (future)
├── e2e/           # Playwright browser tests
└── setup.js       # Test setup configuration

.claude/
├── docs/          # Organized documentation
├── agents/        # Claude agent configurations (auto-detected)
├── commands/      # Claude command configurations (auto-detected)
├── settings.local.json  # Permissions configuration
└── mcp-servers.json     # MCP server configuration

.temp/             # Temporary files for QA outputs, screenshots, test results
```

## Claude Documentation Structure

- **CLAUDE.md** - Main project overview and quick references
- **.claude/docs/ARCHITECTURE.md** - Detailed architecture information
- **.claude/docs/TESTING.md** - Testing strategies and commands
- **.claude/docs/QA_PROTOCOLS.md** - QA testing protocols
- **.claude/docs/FILE_ORGANIZATION.md** - This file
- **QA_TESTING_GUIDE.md** - Comprehensive QA agent guide
- **TESTING_GUIDE.md** - In-game testing system documentation

## Temporary Files

- **Location**: `.temp/` directory (ignored by git)
- **Purpose**: Store temporary test files, screenshots, quick experiments, test results
- **Usage**: Always use `.temp/` directory instead of putting temporary files in project root