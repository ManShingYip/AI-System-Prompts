# Copilot Memory System

## Purpose of This Folder

This is the **GitHub Copilot Memory System Folder**, used for:
- Recording important project decisions
- Tracking task progress
- Preventing AI from peeking at other potentially outdated .md files

---

## Why Do We Need This Folder?

### Problem: AI Memory Confusion
If AI reads all .md files in the project randomly:
- ❌ May read outdated documentation (e.g., old README)
- ❌ May read experimental notes (inaccurate information)
- ❌ Leads to AI giving incorrect suggestions

### Solution: Whitelist Mechanism
- ✅ AI only reads files in this folder
- ✅ All files have timestamps (for reliability assessment)
- ✅ Non-whitelist files require user permission to read

---

## Folder Structure

```
.copilot-memory/
├── README.md                    # This documentation file
├── PROJECT_OVERVIEW.md          # Project overview (manually updated)
├── TASKS/                       # Completed task records
│   └── YYYY-MM-DD_Task-Name.md
└── DECISIONS/                   # Important decision records
    └── YYYY-MM-DD_Decision-Topic.md
```

---

## File Naming Conventions

**Timestamp Format**: `YYYY-MM-DD_Complete-Description.md`

**Examples**:
- `2025-12-17_Refactor-Login-System.md`
- `2025-12-15_Choose-React-Over-Vue.md`

**Why Timestamps?**
- Older files = More likely outdated
- AI prioritizes files within the last 7 days
- Files older than 90 days are considered unreliable

---

## How to Use

### 1. At the Start of Each AI Conversation
```
1. Read conversation-summary (automatically provided by Copilot)
2. Read .copilot-memory/PROJECT_OVERVIEW.md
3. If there's a specific task, search files within last 7 days in TASKS/
4. ❌ Forbidden to randomly browse other .md files
```

### 2. When Task is Completed
AI will ask:
> "Should I record this task to the memory system?"

After your consent, AI will create:
```
.copilot-memory/TASKS/2025-12-17_Complete-Login-Feature.md
```

### 3. For Major Decisions
AI will ask:
> "This is an important decision, should I record it?"

After your consent, AI will create:
```
.copilot-memory/DECISIONS/2025-12-17_Choose-PostgreSQL-Database.md
```

---

## Whitelist vs Blacklist

### ✅ Whitelist (AI Can Read)
- All files in `.copilot-memory/` folder
- Files explicitly requested by user
- Code files necessary for task execution

### ❌ Blacklist (AI Forbidden to Read, Unless User Approves)
- .md files outside `.copilot-memory/`
- Personal notes (e.g., `notes.md`, `TODO.md`)
- Potentially outdated documentation (e.g., old `STRUCTURE.md`)

### Violation Handling
When AI finds a non-whitelist file, it will stop and ask:
> "I noticed [filename], but it's not in the memory system whitelist.  
> This file may be outdated or inaccurate.  
> Do you need me to read this file?"

---

## AI Behavior Transparency

### Core Principle
**AI must notify user before doing anything**

### Notification Examples

**✅ Before Reading Files:**
```
AI: "I need to read .copilot-memory/TASKS/2025-12-15_Refactor-API.md to understand the background."
```

**✅ Before Creating Files:**
```
AI: "Should I record this decision to the memory system?
Create file: .copilot-memory/DECISIONS/2025-12-17_Tech-Stack-Choice.md"
```

**✅ When Finding Non-Whitelist Files:**
```
AI: "I noticed OLD_README.md, but it's not in the whitelist.
Do you need me to read it? (May be outdated)"
```

**❌ Forbidden Behaviors:**
- Secretly reading files without telling user
- Automatically creating memory files without asking
- Pretending not to see non-whitelist files

---

## Time Reliability Assessment

| Time Range | Reliability | AI Behavior |
|-----------|------------|-------------|
| Within 7 days | 90-100% | Prioritize reference |
| 8-30 days | 60-89% | Needs verification |
| 31-90 days | 30-59% | Use with caution |
| 90+ days | <30% | Suggest ignoring |

**Example** (assuming today is 2025-12-17):
- `2025-12-17_Task.md` → 100% reliability (today)
- `2025-12-10_Task.md` → 95% reliability (7 days ago)
- `2025-11-17_Task.md` → 70% reliability (30 days ago, needs verification)
- `2025-09-17_Task.md` → 40% reliability (91 days ago, possibly outdated)

---

## Best Practices

### 1. Regularly Update PROJECT_OVERVIEW.md
- Update when project goals change
- Update when major architecture adjustments occur
- Check at least once a month

### 2. Record After Task Completion
- Record when important features are completed
- Record when complex bugs are solved
- Record when new knowledge is learned

### 3. Must Record Major Decisions
- When choosing technology stack
- When making architecture design decisions
- When deciding on refactoring solutions

### 4. Regularly Clean Old Files
- Consider archiving or deleting files older than 90 days
- Keep important decision records
- Delete outdated task records

---

## FAQ

**Q: Why is the folder named `.copilot-memory` (with a dot)?**  
A: Folders starting with a dot are hidden in Unix/Linux, avoiding interference with project structure.

**Q: Can I manually edit these files?**  
A: Yes! These are pure Markdown files, you can manually edit them anytime.

**Q: What if I want AI to read a non-whitelist file?**  
A: Simply tell AI explicitly: "Please read [filename]".

**Q: Does this system operate automatically?**  
A: No. AI will ask you each time if recording is needed, you have complete control.

---

**Last Updated**: 2025-12-17  
**Maintainers**: Query/Action/Solo Framework Development Team
