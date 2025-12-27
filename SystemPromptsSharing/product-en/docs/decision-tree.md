# Decision Tree

> **Note**: This is a simplified English version. For the complete Chinese version with detailed diagrams, see [product-zh-TW/docs/decision-tree.md](../../product-zh-TW/docs/decision-tree.md).

## Overview

This document provides visual decision flows to help users and AI quickly determine which mode to use and how to handle various scenarios.

---

## Main Decision Tree: Mode Selection

```
                    ┌──────────────────┐
                    │   User Input     │
                    └────────┬─────────┘
                             ↓
                    ┌────────────────────┐
                    │ Detect multi-task? │
                    │(keywords/numbers)  │
                    └─────┬──────────┬───┘
                     Yes ↓          ↓ No
        ┌──────────────────┐        │
        │Read/Update        │        │
        │TASK_TRACKER.md    │        │
        └────────┬───────────┘        │
                 ↓                    ↓
                 └────────┬───────────┘
                          ↓
                    ┌────────────────────┐
                    │ Contains trigger?  │
                    └─────┬──────────┬───┘
                          ↓ Yes      ↓ No
            ┌─────────────┴─┐        ↓
            ↓                ↓        ↓
    ┌───────────────┐ ┌──────────────┐ ┌──────────────┐
    │ Solo trigger? │ │Action trigger?│ │Default Query │
    └───┬───────────┘ └──┬───────────┘ └──────┬───────┘
        ↓ Yes           ↓ Yes                 ↓
        │               │                      │
        ↓               ↓                      ↓
┌──────────────┐ ┌──────────────┐ ┌──────────────────┐
│  Solo Mode   │ │ Action Mode  │ │   Query Mode     │
│  Long-running│ │  Execute     │ │   Analysis       │
└──────────────┘ └──────────────┘ └──────────────────┘
```

---

## Query Mode Decision Tree

```
                ┌──────────────────┐
                │ Query Mode Start │
                └────────┬─────────┘
                         ↓
                ┌────────────────────┐
                │Check TASK_TRACKER? │
                │(pending tasks?)    │
                └─────┬──────────┬───┘
                 Yes ↓          ↓ No
        ┌──────────────┐       │
        │Report progress│      │
        │Execute next   │      │
        └────────┬───────┘      │
                 ↓              ↓
                 └──────┬───────┘
                        ↓
                ┌────────────────────┐
                │ Requirements clear?│
                └─────┬──────────┬───┘
                 No ↓          ↓ Yes
        ┌──────────────┐       │
        │ Ask clarifying│      │
        │   question    │      │
        └────────┬───────┘      │
                 ↓              ↓
         ┌──────────────┐ ┌──────────────┐
         │ Wait for user│ │Analyze problem│
         │  input       │ └──────┬───────┘
         └──────┬───────┘        ↓
                ↓         ┌──────────────┐
                └────────→│Generate      │
                          │suggestions   │
                          └──────┬───────┘
                                 ↓
                          ┌──────────────┐
                          │Multiple      │
                          │options?      │
                          └─────┬────┬───┘
                            No↓    ↓Yes
                    ┌──────────┐ ┌──────────────┐
                    │Single rec│ │List 2-3      │
                    │          │ │options       │
                    └─────┬────┘ └──────┬───────┘
                          ↓              ↓
                    ┌────────────────────────┐
                    │ Summary-first output   │
                    │ • Title (1 line)       │
                    │ • Summary (1-2 sent)   │
                    │ • Points (max 3)       │
                    └────────┬───────────────┘
                             ↓
                    ┌────────────────────┐
                    │ Wait for response  │
                    └────────────────────┘
```

**Key Decision Points:**
1. **Requirements unclear** → Ask first, don't guess
2. **Multiple solutions** → List options, let user choose
3. **No execution** → Only provide suggestions even if user asks

---

## Action Mode Decision Tree

```
                ┌──────────────────┐
                │Action Mode Start │
                │(trigger detected)│
                └────────┬─────────┘
                         ↓
                ┌────────────────────┐
                │Extract execution   │
                │intent              │
                └────────┬───────────┘
                         ↓
                ┌────────────────────┐
                │ Risk assessment    │
                │  (A/B/C)           │
                └─────┬──────┬───┬───┘
                      ↓      ↓   ↓
              ┌───────┘      │   └───────┐
              ↓              ↓           ↓
    ┌─────────────┐  ┌──────────┐  ┌──────────┐
    │   A-level   │  │ B-level  │  │ C-level  │
    │  Safe exec  │  │ Confirm  │  │ Forbidden│
    └──────┬──────┘  └────┬─────┘  └────┬─────┘
           ↓              ↓              ↓
    ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
    │Generate pre- │ │Detail risks  │ │Refuse +      │
    │exec confirm  │ │& changes     │ │explain reason│
    └──────┬───────┘ └──────┬───────┘ └──────┬───────┘
           ↓              ↓              ↓
    ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
    │Wait for 2nd  │ │Wait for      │ │Return to     │
    │confirmation  │ │explicit auth │ │Query Mode    │
    └──────┬───────┘ └──────┬───────┘ └──────────────┘
           ↓              ↓
           └──────┬───────┘
                  ↓
           ┌──────────────┐
           │User confirms?│
           └──────┬───┬───┘
             Yes ↓   ↓ No
           ┌──────────────┐ ┌──────────────┐
           │Execute change│ │Return to     │
           │              │ │Query Mode    │
           └──────┬───────┘ └──────────────┘
                  ↓
           ┌──────────────┐
           │Validate      │
           └──────┬───┬───┘
             Pass↓   ↓Fail
           ┌──────────────┐ ┌──────────────┐
           │Report success│ │Rollback +    │
           │              │ │report error  │
           └──────────────┘ └──────────────┘
```

---

## Solo Mode Decision Tree

```
                ┌──────────────────┐
                │ Solo Mode Start  │
                │(auth confirmed)  │
                └────────┬─────────┘
                         ↓
                ┌────────────────────┐
                │Create execution    │
                │plan                │
                └────────┬───────────┘
                         ↓
                ┌────────────────────┐
                │List all tasks      │
                │+ Checkpoints       │
                └────────┬───────────┘
                         ↓
                ┌────────────────────┐
                │User confirms plan? │
                └──────┬─────────┬───┘
                  Yes ↓         ↓ No
                      │    ┌──────────────┐
                      │    │Modify plan   │
                      │    │or cancel     │
                      │    └──────────────┘
                      ↓
                ┌──────────────────┐
                │Create initial    │
                │checkpoint        │
                └────────┬─────────┘
                         ↓
                ┌────────────────────┐
                │Execute task N      │
                └────────┬───────────┘
                         ↓
                ┌────────────────────┐
                │Self-test in        │
                │sandbox             │
                └──────┬─────────┬───┘
                  Pass↓         ↓Fail
                      │    ┌──────────────┐
                      │    │Retry 3x      │
                      │    │→Alert if fail│
                      │    └──────────────┘
                      ↓
                ┌──────────────────┐
                │Deploy to main    │
                │environment       │
                └────────┬─────────┘
                         ↓
                ┌────────────────────┐
                │Report progress     │
                │(N/Total completed) │
                └────────┬───────────┘
                         ↓
                ┌────────────────────┐
                │More tasks?         │
                └──────┬─────────┬───┘
                  Yes ↓         ↓ No
                      │    ┌──────────────┐
                      │    │Final summary │
                      │    │+ Audit log   │
                      │    └──────────────┘
                      ↓
         ┌────────────────────────┐
         │Create checkpoint       │
         │(every 10 tasks)        │
         └────────┬───────────────┘
                  ↓
           ┌──────────────┐
           │Health check  │
           │every 30 min  │
           └──────┬───┬───┘
             OK ↓     ↓ Alert
                │  ┌──────────────┐
                │  │Pause + notify│
                │  └──────────────┘
                ↓
         (Loop back to Execute task N)
```

---

## References

- [Architecture Documentation](./architecture.md)
- [Risk Matrix](./risk-matrix.md)
- [Use Cases](./use-cases.md)
- [System Prompts](../prompts/)

---

**Last Updated**: 2025-12-17  
**Framework Version**: Query/Action/Solo v1.1
