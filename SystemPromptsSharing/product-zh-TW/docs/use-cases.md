# 使用案例（Use Cases）

## 概述

本文件提供 Query/Action/Solo 框架的實際應用場景，涵蓋軟件開發常見任務，展示各模式在真實情境中的運作方式。

---

## 案例分類

### 1. 程式碼重構（Refactoring）
### 2. Bug 除錯（Debugging）
### 3. 功能開發（Feature Development）
### 4. CI/CD 自動化（CI/CD Automation）
### 5. 資料處理（Data Processing）
### 6. 文檔撰寫（Documentation）

---

## 案例 1：重構遺留程式碼（Legacy Code Refactoring）

### 情境描述

使用者有一個 5 年前的 Node.js 專案，包含 15 個模組，想要重構為使用 ES6+ 語法並引入 TypeScript。

---

### Query Mode：分析與規劃

**使用者輸入：**
```
這個專案需要重構成 TypeScript，有什麼建議？
```

**AI 回應（Summary-first）：**
```
標題：Legacy Node.js → TypeScript 遷移策略

摘要：建議採用漸進式遷移（Incremental Migration），分 3 階段執行，
預估 2–3 週完成，風險可控。

要點：
1. 階段 1：設定 TypeScript 環境 + 允許 .js 共存
2. 階段 2：逐模組轉換（優先工具函式 → 業務邏輯）
3. 階段 3：啟用嚴格模式 + 移除所有 .js

（詳細步驟需要時可展開）
```

**決策依據：**  
專案規模大（15 模組），一次性遷移風險高（B 級），Query Mode 先提供規劃，等待使用者確認方向。

---

### Action Mode：執行單階段遷移

**使用者輸入：**
```
請執行階段 1：設定 TypeScript 環境
```

**AI 行為：**

1️⃣ **風險評估：**
```
變更內容：
• 新增 tsconfig.json
• 修改 package.json（新增 typescript, @types/node）
• 新增 .gitignore 規則

影響檔案數：3
風險等級：B（修改 package.json 屬於依賴變更）
```

2️⃣ **預執行確認：**
```
即將執行：設定 TypeScript 環境

預定變更：
1. 建立 tsconfig.json（允許 .js 與 .ts 共存）
2. 安裝 typescript, @types/node 至 devDependencies
3. 更新 .gitignore（排除 dist/, *.js.map）

風險等級：B - 需確認

是否繼續？請回覆「請執行」確認。
```

3️⃣ **使用者確認後執行：**
```
執行完成 ✅

變更要點：
• ✅ 建立 tsconfig.json（allowJs: true, outDir: dist）
• ✅ 安裝 typescript@5.3.3, @types/node@20.10.5
• ✅ 更新 .gitignore

影響範圍：
• 修改檔案數：3
• 測試通過率：100%（執行 npm test 驗證）

下一步建議：
可開始轉換第一個模組（建議從 utils/ 開始）

Audit log: #20251217-002
```

---

### Solo Mode：自動化整體遷移

**使用者輸入：**
```
進入 Solo 模式，自動完成階段 2 的所有模組轉換
```

**AI 行為：**

1️⃣ **列出執行計畫：**
```
Solo 模式執行計畫

任務清單：
1. 轉換 utils/ 模組（5 個檔案）
2. 轉換 services/ 模組（6 個檔案）
3. 轉換 controllers/ 模組（4 個檔案）

風險等級：B（影響 15 個檔案，需確認）
預估時間：30–45 分鐘
Checkpoint 間隔：每 5 個檔案

是否繼續？請回覆「進入 Solo 模式」確認。
```

2️⃣ **使用者確認後執行：**

```
Solo 模式啟動 ✅

[10:00] 建立初始 Checkpoint: #CP-001
[10:02] 開始轉換 utils/logger.js → utils/logger.ts
[10:03] Self-test PASS
[10:03] 開始轉換 utils/validator.js → utils/validator.ts
[10:04] Self-test PASS
[10:05] 建立 Checkpoint: #CP-002（完成 5 檔案）

[10:10] Health Check PASS（進度正常，錯誤率 0%）

[10:15] 開始轉換 services/userService.js → services/userService.ts
[10:16] Self-test FAIL（型別錯誤：User 介面未定義）
[10:16] 回滾至 Checkpoint: #CP-002
[10:17] 修正：先建立 types/User.ts 介面
[10:18] 重試轉換 services/userService.ts
[10:19] Self-test PASS

[10:30] 完成所有轉換

最終報告：
• 總變更數：15 個檔案
• 成功轉換：15
• 失敗後修正：1（userService.ts）
• 建立 Checkpoint：3 個
• Audit log: #20251217-003

下一步建議：
可執行階段 3（啟用 strict mode）
```

**關鍵特性：**
- ✅ 自動處理錯誤（Self-test 失敗 → 回滾 → 修正 → 重試）
- ✅ 定期 Checkpoint（每 5 檔案）
- ✅ Health Check（每 10 分鐘）
- ✅ 完整 Audit log

---

## 案例 2：除錯生產環境錯誤（Production Bug Debugging）

### 情境描述

生產環境出現「500 Internal Server Error」，使用者需要快速定位並修正問題。

---

### Query Mode：問題分析

**使用者輸入：**
```
生產環境 API /api/users 回傳 500 錯誤，log 顯示「Cannot read property 'email' of undefined」
```

**AI 回應：**
```
標題：Null Reference 錯誤診斷

摘要：錯誤原因可能是資料庫查詢回傳 null，但程式未處理空值。
建議檢查 getUserById() 函式的錯誤處理。

要點：
1. 檢查 userService.js 第 42 行（可能未處理 user 為 null）
2. 檢查 database 連線狀態（可能查詢失敗）
3. 建議新增 null check 與錯誤回傳

（需要我讀取相關程式碼協助定位嗎？）
```

---

### Action Mode：快速修正

**使用者輸入：**
```
請執行修正：新增 null check
```

**AI 行為：**

1️⃣ **讀取相關程式碼：**
```javascript
// services/userService.js:42
async function getUserById(id) {
  const user = await db.query('SELECT * FROM users WHERE id = ?', [id]);
  return {
    email: user.email,  // ← 錯誤：user 可能為 null
    name: user.name
  };
}
```

2️⃣ **風險評估：**
```
變更內容：
• 修改 userService.js（新增 null check）
• 影響檔案數：1
• 風險等級：A（單一檔案、可逆、有測試覆蓋）
```

3️⃣ **預執行確認：**
```
即將執行：修正 null reference 錯誤

預定變更：
1. 新增 user 為 null 的檢查
2. 回傳 404 Not Found（而非 500）
3. 新增對應的錯誤訊息

風險等級：A - 安全執行

變更預覽：
async function getUserById(id) {
  const user = await db.query('SELECT * FROM users WHERE id = ?', [id]);
  
  if (!user) {
    throw new NotFoundError('User not found');
  }
  
  return {
    email: user.email,
    name: user.name
  };
}

是否繼續？請回覆「請執行」確認。
```

4️⃣ **執行 & 驗證：**
```
執行完成 ✅

變更要點：
• ✅ 新增 null check
• ✅ 改回傳 404（原為 500）
• ✅ Unit test 更新（新增 null 案例）

驗證結果：
• Unit test: 10/10 PASS
• Integration test: PASS
• 手動測試 /api/users/999（不存在）：回傳 404 ✅

建議：
可部署至 staging 環境驗證後再推生產。

Audit log: #20251217-004
```

---

## 案例 3：新增 API Endpoint（Feature Development）

### 情境描述

使用者需要新增一個 RESTful API endpoint：`POST /api/posts` 用於建立文章。

---

### Query Mode：設計討論

**使用者輸入：**
```
需要新增建立文章的 API，有什麼建議？
```

**AI 回應：**
```
標題：RESTful API 設計建議

摘要：建議遵循 REST 規範，使用 POST /api/posts，包含驗證、
權限檢查、資料庫交易。

要點：
1. Endpoint: POST /api/posts（需 JWT 驗證）
2. Request body: { title, content, tags[] }
3. Response: 201 Created + { id, title, createdAt }

需要實作嗎？（回覆「請執行」開始）
```

---

### Action Mode：實作 API

**使用者輸入：**
```
請執行實作
```

**AI 行為：**

1️⃣ **風險評估：**
```
變更內容：
• 新增 routes/posts.js（API 路由）
• 新增 controllers/postController.js（業務邏輯）
• 新增 models/Post.js（資料模型）
• 新增 tests/posts.test.js（測試）
• 修改 app.js（註冊路由）

影響檔案數：5
風險等級：B（新增 API endpoint，需確認）
```

2️⃣ **預執行確認：**
```
即將執行：實作 POST /api/posts

預定變更：
1. 建立 routes/posts.js（定義 POST /api/posts）
2. 建立 controllers/postController.js（createPost 邏輯）
3. 建立 models/Post.js（Mongoose schema）
4. 建立 tests/posts.test.js（包含 5 個測試案例）
5. 修改 app.js（app.use('/api/posts', postsRouter)）

風險等級：B - 需確認

技術細節：
• 驗證：使用 express-validator
• 權限：requireAuth middleware
• 資料庫：MongoDB + Mongoose

是否繼續？請回覆「請執行」確認。
```

3️⃣ **執行 & 驗證：**
```
執行完成 ✅

變更要點：
• ✅ 建立 routes/posts.js（12 行）
• ✅ 建立 controllers/postController.js（45 行）
• ✅ 建立 models/Post.js（23 行）
• ✅ 建立 tests/posts.test.js（80 行，5 案例）
• ✅ 修改 app.js（註冊路由）

驗證結果：
• Unit test: 5/5 PASS
• Integration test: PASS
• 手動測試：
  POST /api/posts → 201 Created ✅
  （無 token）→ 401 Unauthorized ✅
  （缺少 title）→ 400 Bad Request ✅

Audit log: #20251217-005

建議：
可使用 Postman 或 curl 進一步測試。
```

---

## 案例 4：CI/CD Pipeline 優化（Automation）

### 情境描述

使用者的 GitHub Actions workflow 執行時間過長（15 分鐘），希望優化至 5 分鐘內。

---

### Query Mode：分析瓶頸

**使用者輸入：**
```
GitHub Actions 太慢，如何優化？
```

**AI 回應：**
```
標題：CI/CD Pipeline 優化策略

摘要：主要瓶頸可能是 dependency 安裝與測試執行，建議使用快取與平行化。

要點：
1. 啟用 npm cache（節省 2–3 分鐘）
2. 平行執行 lint + test（節省 3–5 分鐘）
3. 僅對變更的檔案執行測試（節省 1–2 分鐘）

需要我實作這些優化嗎？
```

---

### Action Mode：實作優化

**使用者輸入：**
```
請執行實作
```

**AI 行為：**

1️⃣ **風險評估：**
```
變更內容：
• 修改 .github/workflows/ci.yml

影響檔案數：1
風險等級：B（CI/CD 設定檔變更，需確認）
```

2️⃣ **執行 & 回報：**
```
執行完成 ✅

變更要點：
• ✅ 新增 npm cache（actions/cache@v3）
• ✅ 平行執行 lint 與 test（strategy.matrix）
• ✅ 使用 --changed-files flag（僅測試變更）

優化結果：
• 原執行時間：15 分鐘
• 優化後：4.5 分鐘（節省 70%）

驗證：
• 觸發測試 workflow → 4.5 分鐘完成 ✅
• 所有測試通過

Audit log: #20251217-006
```

---

## 案例 5：資料遷移（Data Migration）

### 情境描述

使用者需要將 1,000,000 筆使用者資料從 MySQL 遷移至 PostgreSQL。

---

### Query Mode：遷移規劃

**使用者輸入：**
```
需要遷移百萬筆資料從 MySQL 到 PostgreSQL
```

**AI 回應：**
```
標題：大規模資料遷移方案

摘要：建議使用批次處理（Batch Processing）+ 驗證機制，
預估 2–3 小時完成，零停機時間（Zero Downtime）。

要點：
1. 階段 1：建立 PostgreSQL schema（10 分鐘）
2. 階段 2：批次遷移（每批 10,000 筆，2 小時）
3. 階段 3：資料驗證與比對（30 分鐘）

風險：
• B 級（涉及 DB schema 變更）
• 建議先在 staging 環境測試

需要進入 Solo 模式自動執行嗎？
```

---

### Solo Mode：自動化遷移

**使用者輸入：**
```
進入 Solo 模式，自動完成遷移
```

**AI 行為：**

```
Solo 模式執行計畫

任務清單：
1. 建立 PostgreSQL schema
2. 批次遷移資料（100 批，每批 10,000 筆）
3. 驗證資料一致性

風險等級：B（需確認）
預估時間：2–3 小時
Checkpoint 間隔：每 10 批（100,000 筆）

是否繼續？
```

**執行過程：**
```
[14:00] Solo 模式啟動 ✅
[14:00] 建立初始 Checkpoint: #CP-MIG-001

[14:05] 階段 1：建立 PostgreSQL schema
[14:10] Schema 建立完成 ✅

[14:15] 階段 2：開始批次遷移
[14:15] 批次 1/100（0–10,000）→ PASS
[14:16] 批次 2/100（10,000–20,000）→ PASS
...
[14:30] 批次 10/100 完成，建立 Checkpoint: #CP-MIG-002

[15:00] Health Check PASS（已遷移 500,000 筆，錯誤率 0%）

[15:30] 批次 50/100 完成，建立 Checkpoint: #CP-MIG-005

[16:00] 批次 100/100 完成 ✅

[16:10] 階段 3：資料驗證
[16:10] 比對 MySQL vs PostgreSQL 資料
[16:30] 驗證完成：1,000,000 筆資料一致 ✅

最終報告：
• 總遷移筆數：1,000,000
• 成功：1,000,000
• 失敗：0
• 總耗時：2 小時 30 分鐘
• 建立 Checkpoint：10 個

Audit log: #20251217-007

建議：
可將 MySQL 設為唯讀模式，切換至 PostgreSQL。
```

---

## 案例 6：文檔自動生成（Documentation）

### 情境描述

使用者希望為專案自動生成 API 文檔（OpenAPI/Swagger）。

---

### Action Mode：產生文檔

**使用者輸入：**
```
請執行產生 API 文檔
```

**AI 行為：**

1️⃣ **風險評估：**
```
變更內容：
• 安裝 swagger-jsdoc, swagger-ui-express
• 建立 swagger.js（OpenAPI 設定檔）
• 修改 app.js（註冊 /api-docs 路由）
• 為現有 routes 新增 JSDoc 註解

影響檔案數：8（app.js + 6 route 檔案 + swagger.js）
風險等級：B（修改 8 個檔案）
```

2️⃣ **執行 & 回報：**
```
執行完成 ✅

變更要點：
• ✅ 安裝 swagger-jsdoc@6.2.8, swagger-ui-express@5.0.0
• ✅ 建立 swagger.js（OpenAPI 3.0 設定）
• ✅ 為所有 API routes 新增 JSDoc 註解
• ✅ 註冊 /api-docs 路由

驗證結果：
• 訪問 http://localhost:3000/api-docs → Swagger UI 正常顯示 ✅
• 文檔包含 15 個 endpoints
• 自動產生 Request/Response schemas

建議：
可部署至 production 供前端團隊參考。

Audit log: #20251217-008
```

---

## 案例 7：多任務處理（Multi-task Management）

### 情境描述

使用者在一次對話中提出多個任務，例如「請幫我 (1) 修正登入 bug、(2) 新增密碼重置功能、(3) 更新 API 文檔」。

---

### Query Mode：偵測多任務

**使用者輸入：**
```
請幫我 (1) 修正登入 bug、(2) 新增密碼重置功能、(3) 更新 API 文檔
```

**AI 行為：**

1️⃣ **多任務偵測：**
```
偵測到 3 個任務

任務拆解：
1. 修正登入 bug
2. 新增密碼重置功能
3. 更新 API 文檔

檢查 TASK_TRACKER.md...
```

2️⃣ **建立/更新任務追蹤：**
```
已建立任務追蹤表 → TASK_TRACKER.md

當前任務：
- Task #1：修正登入 bug（高優先級）

任務佇列：
- Task #2：新增密碼重置功能（高優先級）
- Task #3：更新 API 文檔（中優先級）

是否開始執行任務 1？請回覆「請執行」確認。
```

---

### Action Mode：執行任務序列

**使用者輸入：**
```
請執行
```

**AI 行為：**

**執行任務 1：修正登入 bug**
```
[14:30] 執行任務 1/3 ✅

變更要點：
• ✅ 修正 auth.js 中 token 驗證邏輯
• ✅ 新增單元測試覆蓋錯誤情境

驗證結果：
• 測試通過率：100%（12/12 tests）

進度：1/3 已完成（33%）

---

繼續執行任務 2...
```

**執行任務 2：新增密碼重置功能**
```
[14:35] 執行任務 2/3 ✅

變更要點：
• ✅ 新增 POST /auth/reset-password endpoint
• ✅ 整合郵件服務（SendGrid）
• ✅ 建立 reset-password.html 表單

驗證結果：
• API 測試通過 ✅
• 郵件發送成功 ✅

進度：2/3 已完成（67%）

---

繼續執行任務 3...
```

**執行任務 3：更新 API 文檔**
```
[14:40] 執行任務 3/3 ✅

變更要點：
• ✅ 更新 swagger.js 新增 reset-password endpoint
• ✅ 新增 Request/Response schemas
• ✅ 更新 README.md 使用說明

驗證結果：
• Swagger UI 正常顯示 ✅

---

所有任務已完成 ✅

最終摘要：
• 總任務數：3
• 已完成：3
• 成功率：100%
• 總耗時：10 分鐘

變更檔案：
1. src/auth.js（登入修正）
2. src/routes/reset-password.js（新功能）
3. docs/swagger.js（文檔更新）
4. test/auth.test.js（測試新增）

下一步建議：
部署至 staging 環境測試完整流程。

Audit log: #20251217-009
```

---

### TASK_TRACKER.md 完整範例

**執行前：**
```markdown
# 任務追蹤表（Task Tracker）

## 當前任務

無

## 任務佇列

無

## 已完成任務

無
```

**執行中：**
```markdown
# 任務追蹤表（Task Tracker）

## 當前任務

**Task #2**：新增密碼重置功能  
狀態：🔄 進行中  
優先級：高  
開始時間：2025-12-17 14:35

## 任務佇列

### 高優先級
- Task #3：更新 API 文檔（預估 5 分鐘）

## 已完成任務

- [x] Task #1：修正登入 bug（2025-12-17 14:33）
```

**執行後：**
```markdown
# 任務追蹤表（Task Tracker）

## 當前任務

無

## 任務佇列

無

## 已完成任務

- [x] Task #3：更新 API 文檔（2025-12-17 14:42）
- [x] Task #2：新增密碼重置功能（2025-12-17 14:38）
- [x] Task #1：修正登入 bug（2025-12-17 14:33）

## 統計資訊

總任務數：3  
已完成：3  
完成率：100%
```

---

### 多任務偵測關鍵字

**中文：**
- 「和」、「還有」、「以及」、「然後」
- 逗號（、）、分號（；）
- 數字編號（(1)、(2)、1.、2.）

**英文：**
- "and", "also", "then"
- Comma (,), Semicolon (;)
- Numbered lists (1., 2.)

**範例：**
```
✅ "請修正 bug 和新增功能" → 偵測到 2 個任務
✅ "請 (1) 重構、(2) 測試、(3) 部署" → 偵測到 3 個任務
✅ "修正 A，優化 B，更新 C" → 偵測到 3 個任務
❌ "請重構 user module" → 單一任務，無需追蹤
```

---

## 模式選擇總結表

| 情境 | 推薦模式 | 理由 |
|------|---------|------|
| 諮詢最佳實踐 | Query | 無需執行，僅提供建議 |
| 修正單一檔案 bug | Action | 低風險，快速執行 |
| Refactor 跨模組 | Action（單階段）或 Solo（整體） | 依影響範圍選擇 |
| 新增 API endpoint | Action | 需確認設計，單次執行 |
| CI/CD 優化 | Action | 修改設定檔，需確認 |
| 大規模資料遷移 | Solo | 長時運行，需自動化 |
| 自動化測試 | Solo | 重複執行，需監控 |
| 文檔生成 | Action | 一次性任務 |
| 多任務處理 | Query → Action（序列執行） | 使用 TASK_TRACKER.md 追蹤 |

---

## 參考資料

- [README.md](../../README.md)
- [TASK_TRACKER.md](../TASK_TRACKER.md)
- [架構文檔](./architecture.md)
- [風險矩陣](./risk-matrix.md)
- [決策樹](./decision-tree.md)
- [整合範例](../examples/)

---

**最後更新**：2025-12-17  
**框架版本**：Query/Action/Solo v1.0
