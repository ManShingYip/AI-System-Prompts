# ARCHIVE Folder

> **Purpose**: Store tasks older than 90 days (forgettable, weight <10%)

---

## How to Use

1. **AI will remind you to archive**: When task is older than 90 days
2. **File organization format**: `YYYY-QX/task-name.md` (organized by quarter)
3. **AI can forget**: These tasks no longer occupy memory space

---

## Folder Structure Example

```
ARCHIVE/
├── 2024-Q3/
│   ├── 2024-07-15_old-task-A.md
│   └── 2024-08-20_old-task-B.md
├── 2024-Q4/
│   └── 2024-10-10_old-task-C.md
└── 2025-Q1/
    └── 2025-01-05_old-task-D.md
```

---

**Note**:
- After archiving, tasks will be removed from `WEIGHT_CONFIG.md`
- AI completely forgets the existence of these tasks
- If you need to restore, you can manually move files back to `ACTIVE/` or `COMPLETED/`
