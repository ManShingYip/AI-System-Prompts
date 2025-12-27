# Use Cases

> **Note**: This is a simplified English version with key examples. For the complete Chinese version with detailed scenarios, see [product-zh-TW/docs/use-cases.md](../../product-zh-TW/docs/use-cases.md).

## Overview

This document provides practical application scenarios for the Query/Action/Solo Framework, covering common software development tasks and demonstrating how each mode operates in real situations.

---

## Case Categories

1. Code Refactoring
2. Bug Debugging
3. Feature Development
4. CI/CD Automation
5. Data Processing
6. Documentation Writing

---

## Case 1: Legacy Code Refactoring

### Scenario

User has a 5-year-old Node.js project with 15 modules, wants to refactor to ES6+ syntax and introduce TypeScript.

### Query Mode: Analysis & Planning

**User Input:**
```
This project needs to be refactored to TypeScript, any suggestions?
```

**AI Response (Query Mode):**
```
Title: Legacy Node.js → TypeScript Migration Strategy

Summary: Recommend incremental migration, 3 phases, estimated 2-3 weeks,
controllable risk.

Key Points:
1. Phase 1: Setup TypeScript environment + allow .js/.ts coexistence
2. Phase 2: Module-by-module conversion (priority: utils → services → controllers)
3. Phase 3: Enable strict mode + remove all .js files

(Detailed steps available upon request)
```

### Action Mode: Execute Phase 1

**User Input:**
```
Please execute Phase 1: Setup TypeScript environment
```

**AI Behavior:**

**Risk Assessment:**
```
Changes:
• Create tsconfig.json
• Modify package.json (add typescript, @types/node)
• Add .gitignore rules

Affected files: 3
Risk level: B (package.json modification is dependency change)
```

**Pre-execution Confirmation:**
```
About to execute: Setup TypeScript environment

Planned changes:
1. Create tsconfig.json (allow .js and .ts coexistence)
2. Install typescript, @types/node to devDependencies
3. Update .gitignore (exclude dist/, *.js.map)

Risk level: B - Requires confirmation

Continue? Please reply "execute" to confirm.
```

**After User Confirmation:**
```
Execution complete ✅

Changes summary:
• ✅ Created tsconfig.json (allowJs: true, outDir: dist)
• ✅ Installed typescript@5.3.3, @types/node@20.10.5
• ✅ Updated .gitignore

Impact:
• Modified files: 3
• Test pass rate: 100% (verified with npm test)

Next step suggestion:
Can start converting first module (recommend starting from utils/)

Audit log: #20251217-002
```

### Solo Mode: Automated Full Migration

**User Input:**
```
Enter Solo mode, automatically complete Phase 2 module conversions
```

**AI Behavior:**

**Execution Plan:**
```
Solo Mode Execution Plan

Task list:
1. Convert utils/ modules (5 files)
2. Convert services/ modules (6 files)
3. Convert controllers/ modules (4 files)

Risk level: B (affects 15 files, requires confirmation)
Estimated time: 30-45 minutes
Checkpoint interval: Every 5 files

Continue? Please reply "enter Solo mode" to confirm.
```

**After Confirmation:**
```
Solo mode started ✅

[10:00] Created initial Checkpoint: #CP-001
[10:02] Started converting utils/logger.js → utils/logger.ts
[10:03] Self-test PASS
[10:05] Created Checkpoint: #CP-002 (5 files completed)

[10:10] Health Check PASS (progress normal, 0% error rate)

[10:15] Started converting services/userService.js
[10:16] Self-test FAIL (Type error: User interface undefined)
[10:17] Auto-fix attempt 1/3...
[10:18] Self-test PASS
[10:19] Continue execution

[10:45] All tasks completed ✅

Final summary:
• Total files converted: 15
• Successful: 15
• Failed (after retry): 0
• Total time: 45 minutes
• Created checkpoints: 3

All unit tests: PASS
Integration tests: PASS

Next step suggestion:
Can proceed to Phase 3 (enable strict mode)

Audit log: #20251217-SOLO-001
```

---

## Case 2: Bug Debugging

### Scenario

Production environment shows "500 Internal Server Error", user needs to quickly locate and fix the issue.

**Error Message:**
```
[ERROR] 2025-12-17 14:30:15 - Internal Server Error at /api/users/123
TypeError: Cannot read property 'email' of undefined
    at getUserById (services/userService.js:42:18)
```

### Query Mode: Problem Analysis

**User Input:**
```
Production API /api/users/123 returns 500 error,
log shows "Cannot read property 'email' of undefined"
```

**AI Response:**
```
Title: Null Reference Error Diagnosis

Summary: Error likely caused by database query returning null/undefined,
but code doesn't handle null values. Recommend checking getUserById() error handling.

Key Points:
1. Check userService.js:42 (likely not handling user = null)
2. Check database connection status (query may have failed)
3. Recommend adding null check and error return

Decision Basis:
Based on stack trace pointing to userService.js:42, accessing user.email
causes error, typical null reference problem.

Need me to read related code to help locate? (Reply "yes" or directly say "execute fix")
```

### Action Mode: Execute Fix

**User Input:**
```
Execute fix
```

**AI Response:**
```
Risk Assessment:
• Modify services/userService.js (add null check)
• Affected files: 1
• No API interface changes
• No DB schema changes

Risk level: A (safe auto-execution)

Executing fix...

Modified code (lines 40-50):
```javascript
async function getUserById(id) {
  const user = await db.query('SELECT * FROM users WHERE id = ?', [id]);
  
  // Added null check
  if (!user) {
    throw new NotFoundError(`User ${id} not found`);
  }
  
  return {
    email: user.email,
    name: user.name
  };
}
```

Execution complete ✅

Changes:
• Added null check (line 43-45)
• Throw NotFoundError instead of 500 error
• API will now return 404 status code

Validation:
• Unit tests: PASS
• Integration tests: PASS
• Manual test /api/users/999: Returns 404 ✅

Audit log: #20251217-003
```

---

## Case 3: Feature Development

### Scenario

User wants to add user authentication feature, including login, logout, JWT token management.

### Query Mode: Feature Planning

**User Input:**
```
Need to add user authentication, including login/logout
```

**AI Response:**
```
Title: User Authentication Feature Implementation Plan

Summary: Recommend using JWT (JSON Web Token) for stateless authentication,
estimated 2-3 days to complete, requires database table and API endpoints.

Key Points:
1. Database: Add sessions table (store refresh tokens)
2. Backend: Implement login/logout/refresh endpoints
3. Security: Password hashing (bcrypt) + JWT signing

Architecture recommendation:

**Database Schema:**
- users table (username, password_hash, email, created_at)
- sessions table (user_id, refresh_token, expires_at)

**API Endpoints:**
- POST /auth/login (return access_token + refresh_token)
- POST /auth/logout (invalidate refresh_token)
- POST /auth/refresh (renew access_token)

**Security measures:**
- Use bcrypt for password hashing
- Access token: 15 min expiry
- Refresh token: 7 day expiry
- Store refresh tokens in database (revocable)

Need implementation? Reply "execute" or enter Solo mode for full automation.
```

### Solo Mode: Full Feature Implementation

**User Input:**
```
Enter Solo mode, implement complete authentication feature
```

**Execution Plan:**
```
Solo Mode Execution Plan

Phases:
1. Database migration (create tables)
2. Implement auth service layer
3. Implement API endpoints
4. Add middleware (authentication check)
5. Write tests

Risk level: B (affects 8+ files, requires confirmation)
Estimated time: 2-3 hours
Checkpoints: After each phase

Continue?
```

**(Execution process similar to Case 1, omitted for brevity)**

---

## Case 4: CI/CD Automation

### Query Mode: Setup Planning

**User Input:**
```
Want to add GitHub Actions for auto-testing and deployment
```

**AI Response:**
```
Title: GitHub Actions CI/CD Pipeline

Key workflows recommended:
1. Test workflow (on every push/PR)
2. Deploy workflow (on merge to main)
3. Security scan workflow (weekly)

(Details available upon request)
```

### Action Mode: Create Workflow

**User Input:**
```
Execute: Create test workflow
```

**(Execution details omitted for brevity)**

---

## Summary Table

| Case | Best Mode | Reason |
|------|-----------|--------|
| Legacy refactoring (single file) | Action | B-level risk, needs confirmation |
| Legacy refactoring (full project) | Solo | Long-running, multi-checkpoint |
| Bug fix (single file) | Action | A-level risk, safe auto-exec |
| Bug diagnosis | Query | Analysis phase, no execution |
| New feature (small) | Action | B-level risk, single execution |
| New feature (large) | Solo | Multi-step, needs tracking |
| CI/CD setup | Action | Config file changes, B-level |
| Documentation update | Action | A-level risk, safe |

---

## Best Practices

### When to Use Query Mode
- ✅ Exploring solutions
- ✅ Code review
- ✅ Architecture planning
- ✅ Learning new concepts

### When to Use Action Mode
- ✅ Single file fixes
- ✅ Small feature additions
- ✅ Config updates
- ✅ Documentation changes

### When to Use Solo Mode
- ✅ Large refactoring
- ✅ Multi-module migrations
- ✅ Batch processing
- ✅ Full feature implementation

---

## References

- [Architecture Documentation](./architecture.md)
- [Decision Tree](./decision-tree.md)
- [Risk Matrix](./risk-matrix.md)
- [System Prompts](../prompts/)

---

**Last Updated**: 2025-12-17  
**Framework Version**: Query/Action/Solo v1.1
