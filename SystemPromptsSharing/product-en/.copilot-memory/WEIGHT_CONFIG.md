# Task Weight Configuration

> **Purpose**: Track all task weights (priorities). AI uses weight to determine memory strategy.

---

## Weight Rules

- **100%**: Just proposed (< 3 hours)
- **95%**: 3-6 hours ago
- **90%**: 6-24 hours ago
- **85%**: 1-3 days ago
- **75%**: 3-7 days ago
- **65%**: 7-16 days ago
- **50%**: 16-30 days ago
- **35%**: 30-60 days ago
- **20%**: 60-90 days ago
- **<10%**: 90+ days (can be archived)

For detailed time configuration, see: [TIME_CONFIG.md](TIME_CONFIG.md)

---

## High-Weight Tasks (≥80%)

> **AI Behavior**: Remember all details completely (dynamic memory)

| Task Name | File Path | Weight | Created Time |
|-----------|-----------|--------|--------------|
| _Example: No tasks_ | _ACTIVE/YYYY-MM-DD_HH-MM_task-name.md_ | _100%_ | _YYYY-MM-DD HH:MM_ |

---

## Medium-Weight Tasks (50%-80%)

> **AI Behavior**: Remember summary only (static memory/compressed)

| Task Name | File Path | Weight | Completed Time |
|-----------|-----------|--------|----------------|
| _Example: No tasks_ | _COMPLETED/YYYY-MM-DD_task-name_summary.md_ | _75%_ | _YYYY-MM-DD_ |

---

## Low-Weight Tasks (<50%)

> **AI Behavior**: Can forget (archive)

| Task Name | File Path | Weight | Archived Time |
|-----------|-----------|--------|---------------|
| _Example: No tasks_ | _ARCHIVE/YYYY-QX/task-name.md_ | _<10%_ | _YYYY-MM-DD_ |

---

## AI Reminder Rules

1. **Before each conversation**: Check high-weight task count
2. **If ≥3 active tasks found**: Remind user to adjust priorities
3. **When creating new task**: Automatically add to high-weight list (100%)
4. **When task completes**: Move to medium-weight (decay based on time)
5. **For tasks older than 90 days**: Remind user to archive

---

**Last Updated**: YYYY-MM-DD
