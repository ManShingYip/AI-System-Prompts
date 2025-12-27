# Query/Action/Solo Architecture Design Documentation

> **Note**: This is the English version. For the complete Chinese version with all detailed examples and diagrams, see [product-zh-TW/docs/architecture.md](../../product-zh-TW/docs/architecture.md).

## Overview

This document details the architecture design, technical implementation, and design decisions of the Query/Action/Solo three-mode framework.

---

## Design Philosophy

### Core Problems

Traditional AI assistants face these challenges:
1. **Insufficient Security**: Easy to accidentally execute high-risk operations
2. **Poor Controllability**: Difficult to predict AI behavior
3. **Low Stability**: Long-running tasks prone to errors or hallucinations
4. **Low Communication Efficiency**: Responses either too verbose or too brief

### Design Principles

1. **Safety First**: Conservative by default, execute only with explicit authorization
2. **Mode Separation**: Clear distinction between query and execution
3. **Double Confirmation**: High-risk operations require secondary authorization
4. **Observability**: Checkpoint, audit log, rollback capabilities
5. **Information Density**: Respond in user's language with English technical terms in parentheses

---

## Architecture Diagrams

### Overall Architecture

```
┌─────────────────────────────────────────────────┐
│           User Input                             │
└─────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────┐
│       Multi-task Detection                       │
│   Detect: "and", "also", commas, numbering       │
│   → Is multi-task? → Read/Update TASK_TRACKER.md │
└─────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────┐
│         Trigger Word Detection                   │
│   Solo trigger? → Action trigger? → Default Query│
└─────────────────────────────────────────────────┘
                        ↓
        ┌───────────────┼───────────────┐
        ↓               ↓               ↓
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ Query Mode   │ │ Action Mode  │ │  Solo Mode   │
│              │ │              │ │              │
│ • Summary    │ │ • Pre-exec   │ │ • Long-run   │
│ • Suggestions│ │ • Risk level │ │ • Self-test  │
│ • No execute │ │ • Double auth│ │ • Checkpoint │
│ • Task queue │ │ • Progress   │ │ • Tracking   │
└──────────────┘ └──────────────┘ └──────────────┘
        ↓               ↓               ↓
┌─────────────────────────────────────────────────┐
│          TASK_TRACKER.md (Task Tracking)         │
│  • Task queue (Pending/In Progress/Completed)    │
│  • Task priority and dependencies                │
│  • Execution progress and timestamps             │
└─────────────────────────────────────────────────┘
        ↓               ↓               ↓
┌─────────────────────────────────────────────────┐
│           Formatted Output                       │
│   Query: Summary-first format                    │
│   Action: Execution summary + Audit log          │
│   Solo: Progress reports + Checkpoints           │
└─────────────────────────────────────────────────┘
```

### Mode Switching State Machine

```
     ┌─────────────┐
     │    Start    │
     └──────┬──────┘
            ↓
     ┌─────────────┐
  ┌─→│ Query Mode  │←──┐
  │  │  (default)   │   │
  │  └──────┬──────┘   │
  │         │           │
  │  Detect triggers    │
  │         │           │
  │  ┌──────▼──────┐   │
  │  │Action auth? │   │
  │  └──────┬──────┘   │
  │    Yes↓   No        │
  │  ┌──────▼──────┐   │
  │  │ Action Mode │───┘
  │  └──────┬──────┘   Complete/Cancel
  │         │           
  │  Detect Solo auth    
  │         │           
  │  ┌──────▼──────┐   
  │  │ Solo auth?  │   
  │  └──────┬──────┘   
  │    Yes↓   No        
  │  ┌──────▼──────┐   
  └──│  Solo Mode  │───┘
     └─────────────┘   Complete/Pause
```

---

## Mode Details

### Query Mode

**Input Processing Flow:**
```
User Input
    ↓
Semantic Understanding
    ↓
Requirements Clear?
├─ Yes → Analyze Problem → Generate Suggestions
└─ No → Ask Clarifying Question
    ↓
Formatted Output (Summary-first)
    ↓
Wait for User Response
```

**Output Format:**
```markdown
Title: [One-line summary]
Summary: [1-2 key sentences, ≤200 words]
Key Points:
1. [Point 1, ≤20 words]
2. [Point 2, ≤20 words]
3. [Point 3, ≤20 words]

(Details not expanded by default)
```

**Prohibited Actions:**
- ❌ Modify any files
- ❌ Execute any system commands
- ❌ Call external tools/APIs
- ❌ Auto-create report files
- ❌ Output internal monologue (chain-of-thought)

---

### Action Mode

**Execution Flow:**
```
Detect Authorization Word
    ↓
Extract Execution Intent
    ↓
Risk Assessment (A/B/C)
    ↓
┌─────────────────┐
│ Risk Level?     │
│ A / B / C       │
└────┬────────────┘
     │
     ├─ A → Generate pre-exec confirmation → Wait → Execute
     ├─ B → Detailed risk explanation → Wait for explicit auth → Execute
     └─ C → Refuse + Explain reason
          ↓
     Execute & Validate
          ↓
     ┌─────────┐
     │Pass?    │
     └────┬────┘
          ├─ Yes → Report success summary
          └─ No → Rollback to checkpoint → Report error
```

**Risk Assessment Algorithm:**
```python
def assess_risk(changes):
    # C-level checks (highest priority)
    if has_sensitive_data(changes):
        return 'C'
    if has_permission_change(changes):
        return 'C'
    if has_deletion(changes):
        return 'C'
    
    # B-level checks
    if file_count(changes) > 3:
        return 'B'
    if has_api_change(changes):
        return 'B'
    if has_db_schema_change(changes):
        return 'B'
    if has_breaking_change(changes):
        return 'B'
    
    # A-level (safe by default)
    return 'A'
```

**Validation Mechanism:**
```
Execute Changes
    ↓
Internal Validation (JSON schema validation)
    ↓
External Validation (Unit tests / Integration tests)
    ↓
┌─────────┐
│All Pass?│
└────┬────┘
     ├─ Yes → Commit & Report
     └─ No → Rollback & Report Error
          ↓
     Failed 3 times?
          ├─ Yes → Switch to Query Mode + Alert
          └─ No → Wait for User Instruction
```

---

### Solo Mode

**Long-Running Architecture:**
```
┌─────────────────────────────────────────────┐
│              Solo Controller                │
│  • Task Queue                               │
│  • Progress Tracker                         │
│  • Health Monitor                           │
└─────────────┬───────────────────────────────┘
              ↓
    ┌─────────┴─────────┐
    ↓                   ↓
┌──────────┐      ┌──────────┐
│ Sandbox  │      │ Main Env │
│ Executor │ ───→ │ Deployer │
└────┬─────┘      └──────────┘
     │
     ↓
┌──────────┐      ┌──────────┐
│Self-test │      │Checkpoint│
│  Runner  │      │  Manager │
└──────────┘      └──────────┘
```

**Health Check Mechanism:**
```python
class HealthMonitor:
    def __init__(self):
        self.last_progress_time = now()
        self.error_count = 0
        self.checkpoint_interval = 10  # minutes
        
    def check_health(self):
        # Check 1: Progress timeout
        if now() - self.last_progress_time > 120:  # 2 hours
            self.alert("No progress for 2 hours")
            return False
        
        # Check 2: Error rate too high
        if self.error_count > 5:
            self.alert("Error count exceeded")
            return False
        
        # Check 3: Missing checkpoint
        if not self.has_recent_checkpoint():
            self.alert("Missing checkpoint")
            return False
        
        return True
    
    def on_progress(self):
        self.last_progress_time = now()
        self.error_count = 0
    
    def on_error(self):
        self.error_count += 1
```

**Checkpoint Strategy:**
```python
class CheckpointManager:
    def create_checkpoint(self, state):
        checkpoint = {
            'id': generate_id(),
            'timestamp': now(),
            'state': state,
            'files_modified': get_modified_files(),
            'rollback_script': generate_rollback()
        }
        save_to_external_db(checkpoint)
        return checkpoint['id']
    
    def rollback(self, checkpoint_id):
        checkpoint = load_from_external_db(checkpoint_id)
        execute_script(checkpoint['rollback_script'])
        restore_state(checkpoint['state'])
```

**Self-test Flow:**
```
Every Change
    ↓
┌──────────────┐
│ Sandbox Env  │
│ • Isolated   │
│ • Mock data  │
└──────┬───────┘
       ↓
Execute Unit Tests
       ↓
Execute Integration Tests
       ↓
REPL Validation (syntax check)
       ↓
┌─────────┐
│All Pass?│
└────┬────┘
     ├─ Yes → Deploy to real env → Create Checkpoint
     └─ No → Log error → Rollback → Retry or Alert
```

---

## Technical Implementation

### Authorization Word Detection

```python
class TriggerDetector:
    SOLO_TRIGGERS = {
        'zh': ['進入 Solo 模式', '請自主執行', '自動完成'],
        'en': ['enter solo mode', 'run autonomously', 'auto complete']
    }
    
    ACTION_TRIGGERS = {
        'zh': ['請執行', '開始修改', '請幫我實作'],
        'en': ['implement', 'execute', 'apply changes', 'do it']
    }
    
    def detect_mode(self, user_input):
        # Priority: Solo (highest threshold)
        if any(trigger in user_input.lower() 
               for triggers in self.SOLO_TRIGGERS.values() 
               for trigger in triggers):
            return 'SOLO'
        
        # Secondary: Action
        if any(trigger in user_input.lower() 
               for triggers in self.ACTION_TRIGGERS.values() 
               for trigger in triggers):
            return 'ACTION'
        
        # Default: Query
        return 'QUERY'
```

### Audit Log Format

```json
{
  "log_id": "uuid-v4",
  "timestamp": "2025-12-17T14:30:00Z",
  "mode": "ACTION | SOLO",
  "model": "gpt-4",
  "user_input": "execute refactoring",
  "intent": "refactor module X",
  "risk_level": "A | B | C",
  "changes": [
    {
      "file": "src/module.js",
      "operation": "modify | create | delete",
      "lines_changed": 42
    }
  ],
  "checkpoint_id": "checkpoint_001",
  "validation": {
    "unit_tests": "pass | fail",
    "integration_tests": "pass | fail"
  },
  "result": "success | failure | rollback",
  "error_message": null
}
```

---

## Security Mechanisms

### Defense Layers

```
┌─────────────────────────────────────┐
│  Layer 1: Trigger Detection         │
│  • Explicit authorization required  │
└─────────────┬───────────────────────┘
              ↓
┌─────────────────────────────────────┐
│  Layer 2: Pre-execution Confirmation│
│  • Restate purpose, list changes    │
│  • Wait for secondary confirmation  │
└─────────────┬───────────────────────┘
              ↓
┌─────────────────────────────────────┐
│  Layer 3: Risk Classification       │
│  • A/B/C automatic assessment       │
│  • C-level auto-refuses             │
└─────────────┬───────────────────────┘
              ↓
┌─────────────────────────────────────┐
│  Layer 4: Sandbox Validation        │
│  • Execute in isolated environment  │
│  • Deploy only if self-test passes  │
└─────────────┬───────────────────────┘
              ↓
┌─────────────────────────────────────┐
│  Layer 5: Checkpoint & Audit        │
│  • All changes can be rolled back   │
│  • Complete logs for auditing       │
└─────────────────────────────────────┘
```

---

## Performance Optimization

### Solo Mode Optimization Strategies

**1. Parallel Processing:**
```python
# Split tasks into parallel executable subtasks
tasks = split_into_subtasks(main_task)
results = parallel_execute(tasks, max_workers=4)
```

**2. Batch Checkpoints:**
```python
# Avoid checkpoint on every change (high overhead)
# Create checkpoint every N files instead
if files_processed % 10 == 0:
    create_checkpoint(current_state)
```

**3. Cache Validation Results:**
```python
# If file unchanged, reuse previous test results
test_cache = {}
if file_hash in test_cache:
    return test_cache[file_hash]
```

---

## Extensibility

### Custom Modes

Framework supports adding custom modes:

```python
class CustomMode(BaseMode):
    TRIGGERS = ['custom trigger']
    
    def execute(self, user_input):
        # Custom logic
        pass
```

### External System Integration

**Example: Enterprise Approval System Integration**
```python
class EnterpriseActionMode(ActionMode):
    def pre_execution_check(self, changes):
        risk = self.assess_risk(changes)
        
        if risk in ['B', 'C']:
            # Call external approval API
            approval_id = request_approval(changes)
            wait_for_approval(approval_id)
        
        return super().pre_execution_check(changes)
```

---

## Version Evolution

### v1.1 (Current)
- ✅ Three-mode basic architecture
- ✅ A/B/C risk classification
- ✅ Multi-language support
- ✅ Multi-task management (TASK_TRACKER.md)
- ✅ Memory system with time decay
- ✅ Checkpoint & Audit log

### v2.0 (Planned)
- 🔄 MCP (Model Context Protocol) integration
- 🔄 Multi-agent collaboration support
- 🔄 More fine-grained risk levels (A1/A2/B1/B2)
- 🔄 Web UI control panel

### v3.0 (Future)
- 💡 AI autonomous learning of risk criteria
- 💡 Auto-generate best practice recommendations
- 💡 Seamless cross-language switching

---

## References

- [README.md](../README.md)
- [Decision Tree](./decision-tree.md)
- [Risk Matrix](./risk-matrix.md)
- [Use Cases](./use-cases.md)
- [System Prompts](../prompts/)
- [Integration Examples](../integration/)

---

**Last Updated**: 2025-12-17  
**Framework Version**: Query/Action/Solo v1.1
