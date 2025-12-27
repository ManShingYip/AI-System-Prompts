# 整合範例：VS Code + GitHub Copilot

## 概述

本文件說明如何將 Query/Action/Solo 框架整合至 VS Code 與 GitHub Copilot，讓您的 AI 助手遵循安全可控的三模式運作規範。

---

## 前置需求

### 必要工具
- [VS Code](https://code.visualstudio.com/) v1.85+
- [GitHub Copilot Extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
- GitHub Copilot 訂閱（個人/企業版）

### 可選工具
- [GitHub Copilot Chat](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat)（增強對話體驗）

---

## 整合步驟

### 步驟 1：建立 System Prompt 檔案

**方式 A：使用專案層級設定（推薦）**

在專案根目錄建立 `.github/copilot-instructions.md`：

```bash
# Windows (PowerShell)
New-Item -ItemType Directory -Force -Path .github
New-Item -ItemType File -Path .github/copilot-instructions.md

# macOS/Linux
mkdir -p .github
touch .github/copilot-instructions.md
```

**方式 B：使用全域設定**

在使用者 home 目錄建立 `.copilot-instructions.md`：

```bash
# Windows
New-Item -ItemType File -Path $HOME\.copilot-instructions.md

# macOS/Linux
touch ~/.copilot-instructions.md
```

---

### 步驟 2：複製 System Prompt

開啟 [`prompts/full-solo.md`](../prompts/full-solo.md)，複製完整內容至剛建立的檔案中。

**或使用指令：**

```bash
# 複製至專案層級（推薦）
cp Design_system_prompts/prompts/full-solo.md .github/copilot-instructions.md

# 複製至全域
cp Design_system_prompts/prompts/full-solo.md ~/.copilot-instructions.md
```

---

### 步驟 3：重啟 VS Code

關閉並重新開啟 VS Code，讓 GitHub Copilot 載入新的 system prompt。

**驗證載入成功：**

1. 開啟 VS Code
2. 按下 `Ctrl+Shift+P`（Windows/Linux）或 `Cmd+Shift+P`（macOS）
3. 輸入 `GitHub Copilot: Show Chat`
4. 在 Chat 中詢問：「你現在使用什麼模式？」

**預期回應：**
```
標題：當前模式

摘要：我目前處於 Query Mode（查詢模式），這是預設模式。
若需執行變更，請使用授權詞（如「請執行」）切換至 Action Mode。

要點：
1. Query Mode：分析與建議（預設）
2. Action Mode：執行變更（需授權詞）
3. Solo Mode：長時運行（需 Solo 授權詞）
```

若收到此回應，代表整合成功 ✅

---

## 使用範例

### 範例 1：Query Mode（諮詢）

**使用者輸入：**
```
這個函式如何優化？

async function getUsers() {
  const users = await db.query('SELECT * FROM users');
  return users.map(u => ({
    id: u.id,
    name: u.name
  }));
}
```

**GitHub Copilot 回應：**
```
標題：資料庫查詢優化建議

摘要：建議使用 SELECT 指定欄位、新增索引、分頁查詢，
預估可提升 60% 效能。

要點：
1. 避免 SELECT *（僅選需要的欄位）
2. 新增索引至常查詢欄位
3. 實作分頁（避免一次載入所有資料）

決策依據：
SELECT * 會載入所有欄位（包含不需要的），浪費記憶體與頻寬；
分頁可避免大量資料一次載入。

需要我實作這些優化嗎？（回覆「請執行」）
```

---

### 範例 2：Action Mode（執行變更）

**使用者輸入：**
```
請執行優化
```

**GitHub Copilot 回應：**

```
即將執行：優化資料庫查詢

預定變更：
1. 修改 SQL：SELECT * → SELECT id, name
2. 新增分頁參數（page, limit）
3. 修改函式簽名（新增 options 參數）

風險等級：A - 安全執行

變更預覽：
async function getUsers(options = { page: 1, limit: 10 }) {
  const offset = (options.page - 1) * options.limit;
  const users = await db.query(
    'SELECT id, name FROM users LIMIT ? OFFSET ?',
    [options.limit, offset]
  );
  return users.map(u => ({
    id: u.id,
    name: u.name
  }));
}

是否繼續？請回覆「請執行」確認。
```

**使用者再次輸入：**
```
請執行
```

**GitHub Copilot 執行變更並回報：**
```
執行完成 ✅

變更要點：
• ✅ SELECT * → SELECT id, name
• ✅ 新增分頁參數（page, limit）
• ✅ 新增 LIMIT/OFFSET 語句

驗證結果：
• 修改檔案：services/userService.js
• Unit test: PASS（新增 3 個測試案例）

Audit log: #20251217-OPT-001
```

---

### 範例 3：Solo Mode（自動化任務）

**使用者輸入：**
```
進入 Solo 模式，自動為所有 API endpoints 新增 rate limiting
```

**GitHub Copilot 回應：**

```
Solo 模式執行計畫

任務清單：
1. 安裝 express-rate-limit 套件
2. 建立 rateLimiter middleware
3. 為所有 routes 套用 middleware（8 個 endpoints）
4. 新增設定檔（config/rateLimit.js）
5. 更新測試（新增 rate limit 測試案例）

風險等級：B（影響 8 個檔案，需確認）
預估時間：15–20 分鐘
Checkpoint 間隔：每完成一個步驟

是否繼續？請回覆「進入 Solo 模式」確認。
```

**使用者輸入：**
```
進入 Solo 模式
```

**GitHub Copilot 執行並回報進度：**
```
Solo 模式啟動 ✅

[14:00] 建立初始 Checkpoint: #CP-RATELIMIT-001
[14:01] 安裝 express-rate-limit@7.1.5 ✅
[14:03] 建立 middleware/rateLimiter.js ✅
[14:05] 套用至 routes/users.js ✅
[14:07] 套用至 routes/products.js ✅
...
[14:10] Health Check PASS
[14:15] 所有 routes 已套用 rate limiting ✅

最終報告：
• 總變更數：9 個檔案
• 成功：9
• 測試通過率：100%
• 總耗時：15 分鐘

Audit log: #20251217-RATELIMIT-001

下一步建議：
可測試 rate limiting 是否正常運作（發送 100 requests/min）
```

---

### 範例 4：多任務處理（Multi-task Management）

**使用者輸入：**
```
請幫我 (1) 修正登入 bug、(2) 新增單元測試、(3) 更新 README
```

**GitHub Copilot 回應：**

```
偵測到 3 個任務

任務拆解：
1. 修正登入 bug
2. 新增單元測試
3. 更新 README

已建立任務追蹤表 → TASK_TRACKER.md

當前任務：
- Task #1：修正登入 bug（高優先級）

任務佇列：
- Task #2：新增單元測試（高優先級）
- Task #3：更新 README（中優先級）

是否開始執行任務 1？請回覆「請執行」確認。
```

**使用者輸入：**
```
請執行
```

**GitHub Copilot 執行並回報進度：**
```
[14:30] 執行任務 1/3 ✅

變更要點：
• ✅ 修正 auth.js 中 token 驗證邏輯
• ✅ 修正 session 過期處理

驗證結果：
• 測試通過率：100%

進度：1/3 已完成（33%）

---

[14:35] 執行任務 2/3 ✅

變更要點：
• ✅ 新增 auth.test.js（12 個測試案例）
• ✅ 覆蓋率提升至 85%

驗證結果：
• 測試全部通過 ✅

進度：2/3 已完成（67%）

---

[14:38] 執行任務 3/3 ✅

變更要點：
• ✅ 更新 README.md 登入流程說明
• ✅ 新增單元測試執行指令

---

所有任務已完成 ✅

最終摘要：
• 總任務數：3
• 已完成：3
• 成功率：100%
• 總耗時：8 分鐘

變更檔案：
1. src/auth.js（登入修正）
2. test/auth.test.js（新增測試）
3. README.md（文檔更新）

TASK_TRACKER.md 已更新：所有任務已標記為完成

Audit log: #20251217-MULTITASK-001
```

**TASK_TRACKER.md 自動建立：**

VS Code 會在專案根目錄建立 `TASK_TRACKER.md`：

```markdown
# 任務追蹤表（Task Tracker）

## 當前任務

無

## 任務佇列

無

## 已完成任務

- [x] Task #3：更新 README（2025-12-17 14:38）
- [x] Task #2：新增單元測試（2025-12-17 14:36）
- [x] Task #1：修正登入 bug（2025-12-17 14:33）

## 統計資訊

總任務數：3  
已完成：3  
完成率：100%
```

---

## 進階設定

### 1. 自定義觸發詞

若希望使用自己的觸發詞，可編輯 `.github/copilot-instructions.md`：

```markdown
**中文授權詞：**
- "請執行"
- "開始修改"
- "GO"  ← 新增自定義觸發詞
```

### 2. 調整風險門檻

若專案較小且測試覆蓋率高，可放寬 A 級門檻：

```markdown
- **A 級（安全）：**
  - 影響檔案數 ≤ 5  ← 原為 3
  - 無 API/DB schema 變更
  ...
```

### 3. 整合企業審批系統

若使用企業版 GitHub Copilot，可整合內部審批流程：

```markdown
### 企業審批整合

**若為 B 級風險：**
1. 呼叫企業審批 API
2. 等待審批通過
3. 執行變更

（需自行實作審批 API endpoint）
```

---

## 常見問題排除

### 問題 1：GitHub Copilot 未遵循 System Prompt

**可能原因：**
- System prompt 檔案路徑錯誤
- VS Code 未重啟
- GitHub Copilot 版本過舊

**解決方案：**
1. 確認檔案路徑：`.github/copilot-instructions.md`（專案層級）或 `~/.copilot-instructions.md`（全域）
2. 完全關閉 VS Code 並重啟
3. 更新 GitHub Copilot 至最新版本

---

### 問題 2：觸發詞未生效

**可能原因：**
- 觸發詞拼寫錯誤
- 觸發詞被其他文字包圍

**解決方案：**
確認觸發詞是獨立詞組：

```
✅ "請執行這個變更"     → 偵測到「請執行」
❌ "請重新執行"        → 未偵測到（非完整詞組）
```

---

### 問題 3：Solo Mode 意外中斷

**可能原因：**
- Health Check 失敗
- 錯誤率過高
- 執行超時（> 2 小時無進度）

**解決方案：**
檢查 GitHub Copilot Chat 輸出：

```
[14:30] Health Check FAIL
原因：錯誤率 60%（超過閾值 50%）
已暫停執行並建立 Checkpoint: #CP-XXX

建議：
檢查錯誤訊息並修正問題後，回覆「繼續 Solo 模式」恢復執行。
```

---

## 快捷鍵設定（可選）

可設定快捷鍵快速啟動各模式：

**步驟 1：**  
開啟 VS Code 設定檔（`keybindings.json`）

**步驟 2：**  
新增快捷鍵：

```json
[
  {
    "key": "ctrl+shift+q",
    "command": "github.copilot.chat.sendMessage",
    "args": "你現在處於什麼模式？"
  },
  {
    "key": "ctrl+shift+a",
    "command": "github.copilot.chat.sendMessage",
    "args": "請執行"
  },
  {
    "key": "ctrl+shift+s",
    "command": "github.copilot.chat.sendMessage",
    "args": "進入 Solo 模式"
  }
]
```

**使用方式：**
- `Ctrl+Shift+Q`：查詢當前模式
- `Ctrl+Shift+A`：切換至 Action Mode
- `Ctrl+Shift+S`：切換至 Solo Mode

---

## 完整設定範例

**專案結構：**
```
my-project/
├── .github/
│   └── copilot-instructions.md  ← System Prompt
├── src/
│   ├── services/
│   ├── controllers/
│   └── utils/
├── tests/
└── package.json
```

**copilot-instructions.md 內容：**
複製 [`prompts/full-solo.md`](../prompts/full-solo.md) 完整內容。

**VS Code 設定（.vscode/settings.json）：**
```json
{
  "github.copilot.enable": {
    "*": true
  },
  "github.copilot.advanced": {
    "debug.overrideEngine": "gpt-4"
  }
}
```

---

## 驗證整合成功

執行以下測試驗證整合：

### 測試 1：Query Mode
```
輸入："這個專案如何改善測試覆蓋率？"
預期：AI 回應建議，不執行任何變更
```

### 測試 2：Action Mode
```
輸入："請執行新增一個測試案例"
預期：AI 先詢問確認，再執行變更
```

### 測試 3：Solo Mode
```
輸入："進入 Solo 模式，自動為所有函式新增 JSDoc 註解"
預期：AI 列出執行計畫，等待確認後啟動
```

### 測試 4：多任務處理
```
輸入："請幫我 (1) 重構 utils.js、(2) 新增測試、(3) 更新文檔"
預期：AI 偵測 3 個任務，建立 TASK_TRACKER.md，依序執行並回報進度
```

### 測試 5：風險分級
```
輸入："請執行刪除所有 user 資料"
預期：AI 拒絕執行，標示為 C 級風險
```

若以上測試皆通過，代表整合成功 ✅

---

## 參考資料

- [README.md](../../README.md)
- [TASK_TRACKER.md](../TASK_TRACKER.md)
- [GitHub Copilot 官方文檔](https://docs.github.com/en/copilot)
- [VS Code 整合指南](https://code.visualstudio.com/docs/editor/artificial-intelligence)
- [System Prompt 完整版](../prompts/full-solo.md)
- [架構文檔](../docs/architecture.md)

---

**最後更新**：2025-12-17  
**框架版本**：Query/Action/Solo v1.0
