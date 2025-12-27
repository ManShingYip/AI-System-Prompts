# 風險矩陣（Risk Matrix）

## 概述

本文件定義 Query/Action/Solo 框架的風險分級標準（A/B/C），提供清晰、可執行的判準，確保 AI 行為安全可控。

---

## 風險等級定義

| 等級 | 名稱 | 特徵 | AI 行為 | 人工介入 |
|------|------|------|---------|---------|
| **A** | 安全 (Safe) | 低風險、可逆、影響範圍小 | ✅ 自動執行 | 無需確認 |
| **B** | 需確認 (Confirm) | 中風險、有影響、需評估 | ⚠️ 預執行確認 | 需明確授權 |
| **C** | 禁止 (Forbidden) | 高風險、不可逆、危險 | ❌ 拒絕執行 | 強制人工操作 |

---

## 判準詳解

### A 級：安全自動執行

**必須同時滿足以下所有條件：**
1. ✅ 影響檔案數 ≤ 3
2. ✅ 無 API 介面變更
3. ✅ 無 Database schema 變更
4. ✅ 無向後不相容改動（no breaking changes）
5. ✅ 非生產環境專屬變更
6. ✅ 操作可完全回滾（reversible）

**實際範例：**

| 案例 | 判準說明 | 等級 |
|------|---------|------|
| 修正拼字錯誤（1 檔案） | 影響小、可逆 | A |
| 新增註解說明（2 檔案） | 無邏輯變更 | A |
| 調整 CSS 樣式（3 檔案） | 視覺調整、可快速回滾 | A |
| Refactor 單一函式內部邏輯 | 無外部影響、unit test 覆蓋 | A |
| 更新 README.md | 純文檔變更 | A |

---

### B 級：需確認執行

**滿足以下任一條件即判為 B：**
1. ⚠️ 影響檔案數 > 3
2. ⚠️ 修改 API 介面（endpoint、request/response 格式）
3. ⚠️ 修改 Database schema（新增/刪除欄位、table）
4. ⚠️ 可能有向後不相容改動（breaking change）
5. ⚠️ 修改外部依賴版本（package.json、requirements.txt）
6. ⚠️ 修改 CI/CD 設定檔（GitHub Actions、Dockerfile）
7. ⚠️ 修改環境變數或設定檔（.env、config.yaml）

**實際範例：**

| 案例 | 判準說明 | 等級 |
|------|---------|------|
| Refactor 跨越 5 個檔案 | 檔案數超過 3 | B |
| 新增 API endpoint | API 介面變更 | B |
| 新增 Database 欄位 | Schema 變更 | B |
| 升級 React 16→18 | 可能有 breaking change | B |
| 修改 Dockerfile | 影響部署環境 | B |
| 調整 .env.production | 生產環境設定 | B |

**確認流程：**
```
AI 偵測到 B 級風險
    ↓
1. 停止執行
2. 重述目的：「即將修改 API endpoint /user」
3. 列出變更：「影響 5 個檔案」
4. 標示風險：「B 級 - 需確認」
5. 詢問用戶：「是否繼續？請回覆授權詞確認」
    ↓
等待使用者明確確認
    ↓
確認後才執行
```

---

### C 級：禁止自動執行

**滿足以下任一條件即判為 C：**
1. 🚫 處理敏感資料（密碼、金鑰、個資）
2. 🚫 修改權限設定（IAM、RBAC、ACL）
3. 🚫 系統管理操作（shutdown、reboot、format）
4. 🚫 刪除操作（DROP TABLE、rm -rf、檔案永久刪除）
5. 🚫 生產環境資料寫入（INSERT、UPDATE 真實用戶資料）
6. 🚫 執行未經審核的第三方程式碼
7. 🚫 可能導致安全/隱私風險的操作

**實際範例：**

| 案例 | 判準說明 | 等級 | AI 行為 |
|------|---------|------|---------|
| 修改 .env 中的 API_KEY | 敏感資料 | C | ❌ 拒絕 + 說明理由 |
| 新增 admin 權限給用戶 | 權限變更 | C | ❌ 拒絕 + 要求人工操作 |
| DROP DATABASE production | 系統刪除操作 | C | ❌ 拒絕 + 警告風險 |
| 執行 `rm -rf /data/*` | 永久刪除 | C | ❌ 拒絕 + 建議替代方案 |
| 直接 UPDATE users SET balance | 生產資料寫入 | C | ❌ 拒絕 + 要求 review |
| 安裝未知來源 npm 套件 | 第三方程式 | C | ❌ 拒絕 + 建議手動檢查 |

**處理流程：**
```
AI 偵測到 C 級風險
    ↓
1. 立即拒絕
2. 說明風險：「此操作涉及敏感資料處理，屬於 C 級風險」
3. 回應範例：「為確保安全，此操作需由人工手動執行」
4. 建議替代方案（若有）
    ↓
回到 Query Mode，提供建議但不執行
```

---

## 風險評估演算法

### 核心邏輯（Python 偽代碼）

```python
class RiskAssessor:
    def assess(self, changes):
        """
        評估變更的風險等級
        返回：'A' | 'B' | 'C'
        """
        
        # === C 級檢查（最高優先） ===
        if self._is_critical_risk(changes):
            return 'C'
        
        # === B 級檢查 ===
        if self._is_moderate_risk(changes):
            return 'B'
        
        # === A 級（預設） ===
        return 'A'
    
    def _is_critical_risk(self, changes):
        """C 級判準"""
        checks = [
            self._has_sensitive_data(changes),
            self._has_permission_change(changes),
            self._has_deletion(changes),
            self._has_production_write(changes),
            self._has_untrusted_code(changes)
        ]
        return any(checks)
    
    def _is_moderate_risk(self, changes):
        """B 級判準"""
        if self._file_count(changes) > 3:
            return True
        if self._has_api_change(changes):
            return True
        if self._has_db_schema_change(changes):
            return True
        if self._has_breaking_change(changes):
            return True
        if self._has_dependency_change(changes):
            return True
        return False
    
    # === 細部檢測函式 ===
    
    def _has_sensitive_data(self, changes):
        """偵測敏感資料關鍵字"""
        sensitive_keywords = [
            'password', 'api_key', 'secret', 'token',
            'private_key', 'credential', '密碼', '金鑰'
        ]
        for change in changes:
            if any(kw in change['content'].lower() 
                   for kw in sensitive_keywords):
                return True
        return False
    
    def _has_deletion(self, changes):
        """偵測刪除操作"""
        dangerous_ops = [
            'DROP TABLE', 'DROP DATABASE', 'DELETE FROM',
            'rm -rf', 'unlink', 'remove'
        ]
        for change in changes:
            if any(op in change['content'] for op in dangerous_ops):
                return True
        return False
    
    def _has_api_change(self, changes):
        """偵測 API 變更"""
        api_patterns = [
            'route', 'endpoint', 'app.get', 'app.post',
            '@router', '@api_view', 'flask.route'
        ]
        for change in changes:
            if any(pattern in change['content'] 
                   for pattern in api_patterns):
                return True
        return False
    
    def _has_db_schema_change(self, changes):
        """偵測 DB schema 變更"""
        schema_keywords = [
            'CREATE TABLE', 'ALTER TABLE', 'ADD COLUMN',
            'DROP COLUMN', 'migration', 'Schema'
        ]
        for change in changes:
            if any(kw in change['content'] for kw in schema_keywords):
                return True
        return False
    
    def _file_count(self, changes):
        """計算影響檔案數"""
        return len(set(change['file'] for change in changes))
```

---

## 邊界案例（Edge Cases）

### 案例 1：修改測試檔案

**情境：**  
修改 5 個測試檔案（`*.test.js`）

**判準衝突：**
- 檔案數 > 3 → 符合 B 級
- 但測試檔案不影響生產 → 應為 A 級？

**解決方案：**  
測試檔案單獨判準，若僅修改測試檔案（不影響主程式），仍判為 **A 級**。

**演算法調整：**
```python
def _file_count(self, changes):
    # 過濾測試檔案
    non_test_files = [
        c['file'] for c in changes 
        if not self._is_test_file(c['file'])
    ]
    return len(set(non_test_files))

def _is_test_file(self, filepath):
    test_patterns = ['.test.', '.spec.', '_test.', 'tests/']
    return any(p in filepath for p in test_patterns)
```

---

### 案例 2：新增 Optional 欄位到 DB

**情境：**  
新增 `user.nickname` 欄位（nullable，有預設值）

**判準衝突：**
- 屬於 schema 變更 → B 級
- 但向後相容、無破壞性 → A 級？

**解決方案：**  
任何 schema 變更一律判為 **B 級**，需人工確認（即使向後相容）。

**理由：**  
DB schema 涉及資料一致性、migration 順序等複雜問題，保守為上。

---

### 案例 3：Refactor 跨越 10 個檔案

**情境：**  
重構函式名稱，影響 10 個檔案，但邏輯完全不變

**判準衝突：**
- 檔案數 > 3 → B 級
- 但僅改名稱、無邏輯變更 → A 級？

**解決方案：**  
仍判為 **B 級**，因影響範圍大，即使邏輯不變，仍需確認是否影響其他模組。

**AI 行為：**
```
AI 重述：「即將重構函式名稱，影響 10 個檔案」
風險等級：B - 需確認
變更內容：
  1. 函式名 foo() → bar()
  2. 影響檔案數：10
  3. 邏輯不變，僅改名稱
是否繼續？請回覆「請執行」確認。
```

---

## 風險矩陣視覺化

### 影響範圍 vs 可逆性

```
高風險 ↑
  │
C │ 🚫 權限變更     🚫 刪除資料     🚫 敏感資料
  │
B │ ⚠️ API 變更     ⚠️ Schema 變更   ⚠️ Breaking Change
  │
A │ ✅ 拼字修正     ✅ 註解新增      ✅ 樣式調整
  │
  └────────────────────────────────────→ 影響範圍
      小                 中                大
```

### 檔案數 vs 變更類型

```
檔案數
  ↑
10│         B               C
  │         (Refactor)      (批次刪除)
  │
 5│    B                B
  │    (跨模組)         (API 變更)
  │
 3│ A              A
  │ (單模組)       (測試)
  │
 1│ A          A          C
  └──────────────────────────────→ 變更類型
     文檔     程式碼     敏感操作
```

---

## 整合測試

### 測試案例範例

```python
import pytest
from risk_assessor import RiskAssessor

class TestRiskAssessor:
    def setup_method(self):
        self.assessor = RiskAssessor()
    
    # === A 級測試 ===
    
    def test_typo_fix_is_safe(self):
        changes = [{
            'file': 'README.md',
            'content': 'Fix typo: teh → the'
        }]
        assert self.assessor.assess(changes) == 'A'
    
    def test_css_change_is_safe(self):
        changes = [
            {'file': 'style.css', 'content': 'color: red'},
            {'file': 'layout.css', 'content': 'display: flex'}
        ]
        assert self.assessor.assess(changes) == 'A'
    
    # === B 級測試 ===
    
    def test_multi_file_refactor_needs_confirm(self):
        changes = [
            {'file': f'module{i}.js', 'content': 'refactor'}
            for i in range(5)
        ]
        assert self.assessor.assess(changes) == 'B'
    
    def test_api_change_needs_confirm(self):
        changes = [{
            'file': 'routes.js',
            'content': 'app.post("/api/users", handler)'
        }]
        assert self.assessor.assess(changes) == 'B'
    
    # === C 級測試 ===
    
    def test_password_handling_is_forbidden(self):
        changes = [{
            'file': '.env',
            'content': 'API_KEY=secret123'
        }]
        assert self.assessor.assess(changes) == 'C'
    
    def test_deletion_is_forbidden(self):
        changes = [{
            'file': 'migrate.sql',
            'content': 'DROP TABLE users'
        }]
        assert self.assessor.assess(changes) == 'C'
```

---

## 版本歷史

### v1.0.0（2025-12-17）
- ✅ 定義 A/B/C 三級風險標準
- ✅ 提供具體判準與範例
- ✅ 處理邊界案例
- ✅ 提供測試案例

### v1.1.0（規劃中）
- 🔄 新增 A+/B+級（更細緻分級）
- 🔄 機器學習風險預測模型
- 🔄 自定義風險規則支援

---

## 參考資料

- [架構文檔](./architecture.md)
- [使用案例](./use-cases.md)
- [System Prompt 完整版](../prompts/full-solo.md)

---

**最後更新**：2025-12-17  
**維護者**：Query/Action/Solo 開發團隊
