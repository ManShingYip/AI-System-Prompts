# Copilot System Prompt: Unified Safe Mode (English Version)

> **AI Assistant Behavior Guidelines - Prevent AI from Doing Stupid Things + Intelligent Memory System**

---

## 🎯 Core Design

**Core Principle**: "The AI must continually ask the user and never act on its own."

### Main Features

1. **Forced Ask-Before-Action Mechanism**: Default Query Mode, AI only analyzes, doesn't execute
2. **Intelligent Analysis Workflow**: 5-step deep analysis (task breakdown, risk assessment, solution options)
3. **Memory System Integration**: Automatic task tracking, weight management, learning to forget
4. **Three-Mode Support**: Query / Action / Solo (automatically switches based on authorization words)

---

## 📦 Installation

1. Copy `.github/copilot-instructions.md` to your project's `.github/` folder
2. Copy `.copilot-memory/` folder to your project root
3. Restart VS Code

---

## 📂 Memory System Architecture

```
.copilot-memory/
├── WEIGHT_CONFIG.md      # Task weight configuration (AI reminds user to adjust priorities)
├── TIME_CONFIG.md        # Time decay configuration (9 thresholds)
├── ACTIVE/               # Active tasks (dynamic memory, weight ≥80%)
├── COMPLETED/            # Completed tasks (static memory, weight 50%-80%)
└── ARCHIVE/              # Tasks older than 90 days (forgettable, weight <10%)
```

### How Memory System Works

1. **Propose new task** → AI creates file in `ACTIVE/`, weight 100%
2. **Task completes** → AI compresses to summary, moves to `COMPLETED/`
3. **Older than 90 days** → AI reminds to archive to `ARCHIVE/`, can forget

---

## 🚀 How to Use

### Query Mode (Default Mode)

AI only analyzes, doesn't execute, suitable for daily use:

```
User: How to optimize this code?
AI:
## Problem Restatement
You want to improve performance.

## Deep Analysis
[Task breakdown, potential issues, dependencies, risk assessment]

## Solution Options
Solution A: Refactoring (Recommended ⭐⭐⭐⭐⭐)
Solution B: Cache optimization

🤔 Please choose a solution, or tell me if you need more detailed explanation.

---
❓ Would you like to record this conversation as a task in the memory system?
[ ] Yes — record as a task
[ ] No — this is a normal Q&A
```

## 🔧 Custom Configuration

### Modify Memory System Time Thresholds

Edit `.copilot-memory/TIME_CONFIG.md`:

```markdown
- 3h → 100% (just proposed)
- 6h → 95%
- 1d → 90%
...
```

### Modify AI Behavior Rules

Edit `.github/copilot-instructions.md` (backup first recommended)

---

## ❓ FAQ

### Q1: Why doesn't AI execute my request?

A: Default is Query Mode. Requires explicit authorization words (like "please execute") to switch to Action Mode.

### Q2: Will the memory system automatically create files?

A: Yes! AI will ask after user proposes a task: "Record as a task?" and only create after user consent.

### Q3: How to clear the memory system?

A: Delete files inside `.copilot-memory/ACTIVE/` and `COMPLETED/`.

### Q4: Can I disable the memory system?

A: Yes. Edit `.github/copilot-instructions.md` and remove the "Memory System Rules" section.

---

## 📜 License

MIT License

---

## 🙏 Contributing

Issues and Pull Requests are welcome!

See: [CONTRIBUTING.md](CONTRIBUTING.md)

---

**Last Updated**: 2025-12-17
**Version**: v2.0 (Unified Safe Mode)
