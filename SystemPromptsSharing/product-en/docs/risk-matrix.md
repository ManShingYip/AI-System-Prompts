# Risk Matrix

> **Note**: This is a simplified English version. For the complete Chinese version with detailed examples, see [product-zh-TW/docs/risk-matrix.md](../../product-zh-TW/docs/risk-matrix.md).

## Overview

This document defines the risk classification standards (A/B/C) for the Query/Action/Solo Framework, providing clear and executable criteria to ensure AI behavior is safe and controllable.

---

## Risk Level Definitions

| Level | Name | Characteristics | AI Behavior | Human Intervention |
|-------|------|-----------------|-------------|-------------------|
| **A** | Safe | Low risk, reversible, small impact | ✅ Auto-execute | No confirmation needed |
| **B** | Confirm | Medium risk, has impact, needs evaluation | ⚠️ Pre-execution confirmation | Requires explicit authorization |
| **C** | Forbidden | High risk, irreversible, dangerous | ❌ Refuse execution | Mandatory manual operation |

---

## Criteria Details

### A-Level: Safe Auto-Execution

**Must satisfy ALL of the following conditions:**
1. ✅ Affected files ≤ 3
2. ✅ No API interface changes
3. ✅ No database schema changes
4. ✅ No breaking changes
5. ✅ Not production-environment specific changes
6. ✅ Operation is fully reversible

**Examples:**

| Case | Reasoning | Level |
|------|-----------|-------|
| Fix typo (1 file) | Small impact, reversible | A |
| Add comments (2 files) | No logic change | A |
| Adjust CSS styles (3 files) | Visual only, quick rollback | A |
| Refactor single function internals | No external impact, unit test covered | A |
| Update README.md | Documentation only | A |

---

### B-Level: Requires Confirmation

**Satisfies ANY of the following conditions:**
1. ⚠️ Affected files > 3
2. ⚠️ Modify API interface (endpoint, request/response format)
3. ⚠️ Modify database schema (add/remove columns, tables)
4. ⚠️ Potential breaking changes
5. ⚠️ Modify external dependency versions (package.json, requirements.txt)
6. ⚠️ Modify CI/CD configuration (GitHub Actions, Dockerfile)
7. ⚠️ Modify environment variables or config files (.env, config.yaml)

**Examples:**

| Case | Reasoning | Level |
|------|-----------|-------|
| Refactor across 5 files | File count > 3 | B |
| Add API endpoint | API interface change | B |
| Add database column | Schema change | B |
| Upgrade React 16→18 | Potential breaking changes | B |
| Modify Dockerfile | Affects deployment environment | B |
| Adjust .env.production | Production config | B |

**Confirmation Flow:**
```
AI detects B-level risk
    ↓
1. Stop execution
2. Restate purpose: "About to modify API endpoint /user"
3. List changes: "Affects 5 files"
4. Mark risk: "B-level - Requires confirmation"
5. Ask user: "Continue? Please reply with authorization word to confirm"
    ↓
Wait for explicit user confirmation
    ↓
Execute after confirmation
```

---

### C-Level: Forbidden Auto-Execution

**Satisfies ANY of the following conditions:**
1. 🚫 Handle sensitive data (passwords, keys, personal information)
2. 🚫 Modify permission settings (IAM, RBAC, ACL)
3. 🚫 System admin operations (shutdown, reboot, format)
4. 🚫 Deletion operations (DROP TABLE, rm -rf, permanent file deletion)
5. 🚫 Production data writes (INSERT, UPDATE real user data)
6. 🚫 Execute unreviewed third-party code
7. 🚫 Operations that may cause security/privacy risks

**Examples:**

| Case | Reasoning | Level | AI Behavior |
|------|-----------|-------|-------------|
| Modify API_KEY in .env | Sensitive data | C | ❌ Refuse + explain |
| Add admin permission to user | Permission change | C | ❌ Refuse + require manual |
| DROP DATABASE production | System deletion | C | ❌ Refuse + warn risk |
| Execute `rm -rf /data/*` | Permanent deletion | C | ❌ Refuse + suggest alternative |
| Direct UPDATE users SET balance | Production data write | C | ❌ Refuse + require review |
| Install unknown npm package | Third-party code | C | ❌ Refuse + suggest manual check |

**Processing Flow:**
```
AI detects C-level risk
    ↓
1. Immediately refuse
2. Explain risk: "This operation involves sensitive data, C-level risk"
3. Response example: "For security, this operation requires manual execution"
4. Suggest alternatives (if any)
    ↓
Return to Query Mode, provide suggestions but don't execute
```

---

## Risk Assessment Algorithm

### Core Logic (Python Pseudocode)

```python
class RiskAssessor:
    def assess(self, changes):
        """
        Assess risk level of changes
        Returns: 'A' | 'B' | 'C'
        """
        
        # === C-level checks (highest priority) ===
        if self._is_critical_risk(changes):
            return 'C'
        
        # === B-level checks ===
        if self._is_moderate_risk(changes):
            return 'B'
        
        # === A-level (default) ===
        return 'A'
    
    def _is_critical_risk(self, changes):
        """C-level criteria"""
        checks = [
            self._has_sensitive_data(changes),
            self._has_permission_change(changes),
            self._has_deletion(changes),
            self._has_production_write(changes),
            self._has_untrusted_code(changes)
        ]
        return any(checks)
    
    def _is_moderate_risk(self, changes):
        """B-level criteria"""
        checks = [
            len(changes.files) > 3,
            self._has_api_change(changes),
            self._has_db_schema_change(changes),
            self._has_breaking_change(changes),
            self._has_dependency_change(changes),
            self._has_config_change(changes)
        ]
        return any(checks)
```

### Detection Methods

```python
class RiskDetector:
    def _has_sensitive_data(self, changes):
        """Detect sensitive data operations"""
        sensitive_patterns = [
            r'password\s*=',
            r'api_key\s*=',
            r'secret\s*=',
            r'token\s*=',
            r'\.env',
            r'credentials'
        ]
        return any(
            re.search(pattern, change.content, re.IGNORECASE)
            for change in changes
            for pattern in sensitive_patterns
        )
    
    def _has_permission_change(self, changes):
        """Detect permission modifications"""
        permission_keywords = [
            'GRANT', 'REVOKE', 'chmod', 'chown',
            'IAM', 'RBAC', 'ACL', 'role', 'permission'
        ]
        return any(
            keyword in change.content
            for change in changes
            for keyword in permission_keywords
        )
    
    def _has_deletion(self, changes):
        """Detect deletion operations"""
        deletion_patterns = [
            r'DROP\s+TABLE',
            r'DROP\s+DATABASE',
            r'DELETE\s+FROM',
            r'rm\s+-rf',
            r'\.unlink\(',
            r'\.remove\(',
            r'os\.remove'
        ]
        return any(
            re.search(pattern, change.content, re.IGNORECASE)
            for change in changes
            for pattern in deletion_patterns
        )
```

---

## Special Cases

### 1. Testing Environment vs Production

Same operation, different risk levels in different environments:

| Operation | Test Environment | Production |
|-----------|------------------|------------|
| DROP TABLE users | B (needs confirmation) | C (forbidden) |
| DELETE FROM users | B | C |
| Modify .env file | B | C |
| UPDATE user balance | B | C |

**Detection Method:**
```python
def assess_environment_risk(changes, environment):
    base_risk = assess_base_risk(changes)
    
    if environment == 'production':
        # Upgrade risk level in production
        if base_risk == 'B' and is_data_modification(changes):
            return 'C'
    
    return base_risk
```

### 2. Cumulative Risk

Multiple small changes may accumulate to higher risk:

```python
def assess_cumulative_risk(changes):
    if all(is_low_risk(c) for c in changes):
        if len(changes) <= 3:
            return 'A'
        elif len(changes) <= 10:
            return 'B'  # Too many changes, upgrade to B
        else:
            return 'C'  # Extremely many changes, upgrade to C
```

---

## References

- [Architecture Documentation](./architecture.md)
- [Decision Tree](./decision-tree.md)
- [Use Cases](./use-cases.md)
- [System Prompts](../prompts/)

---

**Last Updated**: 2025-12-17  
**Framework Version**: Query/Action/Solo v1.1
