# Memory System Time Configuration (User Version)

> **Tip**: You can customize these time thresholds based on your development habits.

---

## Current Configuration (Default Values)

### Ultra-Short-Term Memory (Current Session)
- ⏱️ **Within 3 hours**: 100% reliability
- ⏱️ **Within 6 hours**: 95% reliability

### Short-Term Memory (Daily/Weekly)
- 📅 **Within 1 day**: 90% reliability
- 📅 **Within 3 days**: 85% reliability

### Medium-Term Memory (Sprint Cycle)
- 📆 **Within 7 days**: 75% reliability
- 📆 **Within 16 days**: 65% reliability

### Long-Term Memory (Monthly/Quarterly)
- 🗓️ **Within 30 days**: 50% reliability
- 🗓️ **Within 60 days**: 35% reliability
- 🗓️ **Within 90 days**: 20% reliability

### Historical Records (For Reference Only)
- 📚 **90+ days**: <10% reliability

---

## How to Customize?

### Method 1: Directly Modify This File
Edit the time values above, AI will automatically read them.

### Method 2: Temporary Override in Conversation
Tell AI:
> "I'm on a tight deadline today, please change time thresholds to: 1 hour / 2 hours / 4 hours"

### Method 3: Set in Project Overview
Add to `PROJECT_OVERVIEW.md`:
```markdown
## Time Configuration
- Ultra-short-term: 1 hour / 2 hours
- Short-term: 4 hours / 8 hours
```

---

## Why These Time Ranges?

### 3 Hours / 6 Hours
Suitable for **Intensive Development**:
- 3 hours = Morning to lunch break
- 6 hours = One day's work (no overtime)

### 1 Day / 3 Days
Suitable for **Short Sprints**:
- 1 day = Complete work day
- 3 days = Agile development short cycle

### 7 Days / 16 Days
Suitable for **Standard Projects**:
- 7 days = One week Sprint
- 16 days = Two-week Sprint + buffer

### 30 / 60 / 90 Days
Suitable for **Long-Term Projects**:
- 30 days = Monthly iteration
- 60 days = First half of quarter
- 90 days = Complete quarter

---

## Recommended Configurations (By Project Type)

### 🔥 Intensive Short-Term Projects
```
1 hour / 2 hours / 4 hours / 8 hours / 1 day
```

### ⚡ Standard Agile Development (Recommended)
```
1 day / 3 days / 7 days / 16 days / 30 days
```

### 🏗️ Long-Term System Development (Default)
```
3 hours / 6 hours / 1 day / 3 days / 7 days / 16 days / 30 days / 60 days / 90 days
```

### 🔧 Maintenance Mode
```
7 days / 30 days / 90 days / 180 days
```

---

## File Naming Examples

### Include Time (Recommended, for intensive development)
```
2025-12-17_15:30_Complete-Login-API.md
```

### Date Only (for general development)
```
2025-12-17_Complete-Login-API.md
```

---

**Detailed Documentation**: See [Development VERSION TIME_CONFIG.md](../../dev/memory/TIME_CONFIG.md)

**Last Updated**: 2025-12-17
