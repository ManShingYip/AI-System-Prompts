# ACTIVE Folder

> **Purpose**: Store active tasks (dynamic memory, weight ≥80%)

---

## How to Use

1. **AI will automatically create task files** (when user proposes new tasks)
2. **File naming format**: `YYYY-MM-DD_HH-MM_task-name.md`
3. **AI remembers completely**: All details will be remembered

---

## Task File Structure Example

```markdown
# Task: [task name]

**Created**: YYYY-MM-DD HH:MM
**Status**: pending / in-progress / completed
**User original request**: [user's words]

## Analysis

[task analysis content]

## Solution Options

[provided options]

## User Decision

[user's choice]

## Execution Result

[result after execution]
```

---

**Note**:
- When task completes, AI will move the file to `COMPLETED/` folder
- And compress to summary (keep only core conclusion ≤200 words)
