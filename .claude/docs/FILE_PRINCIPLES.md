# File Organization Principles

> **Enforcement**: All agents MUST follow these principles when creating/organizing files. Read via `/start`.

## Core Principles

### One Responsibility Per File
- Each file has ONE clear purpose
- If you need "and" to describe it, split it
- Utilities are grouped by domain, not dumped in one file

### Group By Feature, Not Type
```
# DO: Feature-based
src/
├── auth/
│   ├── auth.service.ts
│   ├── auth.controller.ts
│   └── auth.test.ts
├── users/
│   ├── user.service.ts
│   └── user.controller.ts

# DON'T: Type-based (harder to navigate)
src/
├── services/
│   ├── auth.service.ts
│   └── user.service.ts
├── controllers/
│   ├── auth.controller.ts
│   └── user.controller.ts
```

### Clear Naming
| Type | Convention | Example |
|------|------------|---------|
| Services | `*.service.ts` | `user.service.ts` |
| Controllers | `*.controller.ts` | `auth.controller.ts` |
| Middleware | `*.middleware.ts` | `rate-limit.middleware.ts` |
| Tests | `*.test.ts` or `*.spec.ts` | `user.service.test.ts` |
| Types/Interfaces | `*.types.ts` | `api.types.ts` |
| Utilities | `*.utils.ts` | `date.utils.ts` |
| Constants | `*.constants.ts` | `config.constants.ts` |

## Directory Structure

### Standard Directories
```
src/              # Source code
tests/            # Test files (if not co-located)
tools/            # Development utilities
  scripts/        # Reusable scripts (version controlled)
  tmp/            # Disposable files (gitignored)
docs/             # Documentation
config/           # Configuration files
```

### Temp Files
- **Always use designated temp directory** (e.g., `tools/tmp/`)
- **Never in project root**
- **Gitignored** - temp files don't belong in version control

## Anti-Patterns

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| `utils.ts` with 50 functions | Monolith, hard to find things | Split by domain: `date.utils.ts`, `string.utils.ts` |
| Files > 300 lines | Too much responsibility | Split into focused modules |
| Deeply nested folders | Hard to navigate | Max 4 levels deep |
| Inconsistent naming | Confusing | Pick convention, stick to it |
| Test files far from source | Context switching | Co-locate or mirror structure |
| Config scattered everywhere | Hard to manage | Centralize in `config/` |

## Import Organization

```typescript
// 1. External packages
import express from 'express';
import { PrismaClient } from '@prisma/client';

// 2. Internal absolute imports
import { UserService } from '@/services/user.service';
import { config } from '@/config';

// 3. Relative imports
import { validateInput } from './validators';
import { UserDTO } from './types';
```

## When to Create New Files

**Create new file when:**
- New distinct responsibility
- File approaching 300 lines
- Mixing unrelated concerns
- Need independent testing

**Keep in same file when:**
- Tightly coupled (always used together)
- Small helper only used by one function
- Would create file < 20 lines

## Checklist

- [ ] One clear responsibility per file
- [ ] Grouped by feature, not type
- [ ] Consistent naming convention
- [ ] Temp files in designated directory
- [ ] No files > 300 lines
- [ ] Imports organized (external → internal → relative)
