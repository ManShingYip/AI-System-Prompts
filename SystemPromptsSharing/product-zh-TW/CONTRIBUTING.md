# Contributing to Query/Action/Solo Framework

## 歡迎貢獻！

感謝您對 Query/Action/Solo 框架的興趣！我們歡迎各種形式的貢獻。

---

## 貢獻方式

### 1. 回報問題（Issues）

若您發現 bug 或有功能建議，請開啟 Issue：

**Bug 回報：**
- 標題：簡短描述問題
- 內容：
  - 預期行為
  - 實際行為
  - 重現步驟
  - 環境資訊（OS、VS Code 版本、GitHub Copilot 版本）

**功能建議：**
- 標題：簡短描述功能
- 內容：
  - 使用情境
  - 預期效果
  - 可能的實作方式

---

### 2. 提交 Pull Request

**步驟：**

1. **Fork 專案**
   ```bash
   # 點擊 GitHub 頁面右上角 "Fork" 按鈕
   ```

2. **Clone 到本地**
   ```bash
   git clone https://github.com/YOUR_USERNAME/Design_system_prompts.git
   cd Design_system_prompts
   ```

3. **建立分支**
   ```bash
   git checkout -b feature/your-feature-name
   ```

4. **進行修改**
   - 修改程式碼或文檔
   - 確保遵循風格指南（見下方）

5. **Commit 變更**
   ```bash
   git add .
   git commit -m "feat: 新增 XXX 功能"
   ```

   **Commit 訊息格式：**
   - `feat:` 新增功能
   - `fix:` 修正 bug
   - `docs:` 文檔變更
   - `refactor:` 重構
   - `test:` 新增測試
   - `chore:` 其他變更

6. **Push 到 GitHub**
   ```bash
   git push origin feature/your-feature-name
   ```

7. **開啟 Pull Request**
   - 前往您的 fork 頁面
   - 點擊 "New Pull Request"
   - 填寫 PR 描述（說明變更內容與目的）

---

### 3. 改善文檔

文檔改善永遠歡迎！包括：
- 修正拼字或語法錯誤
- 補充範例
- 改善說明清晰度
- 翻譯至其他語言

---

## 風格指南

### 文檔風格

1. **語言**
   - 預設使用繁體中文
   - 專業術語保留英文並加括號說明
   - 範例：`refactoring（重構）`

2. **格式**
   - 使用 Markdown
   - 標題採用 `#`、`##`、`###` 階層
   - 程式碼區塊使用 ` ```語言 `

3. **結構**
   - 每個文件包含：概述、內容、參考資料
   - 使用清晰的段落標題
   - 提供具體範例

### 程式碼風格（若有）

1. **Python**
   - 遵循 PEP 8
   - 使用 type hints
   - 函式包含 docstring

2. **JavaScript/TypeScript**
   - 使用 ESLint
   - 使用 Prettier 格式化
   - 變數使用 `const`/`let`（避免 `var`）

---

## 測試

若您的變更涉及程式碼：
- 新增對應的測試案例
- 確保所有測試通過
- 測試覆蓋率 > 80%

---

## Code Review 流程

1. 提交 PR 後，維護者會進行 review
2. 若需修改，會在 PR 中留言
3. 修改後 push 至同一分支，PR 會自動更新
4. Review 通過後，維護者會 merge PR

---

## 行為準則（Code of Conduct）

### 我們的承諾

為營造開放且友善的環境，我們承諾：
- 尊重不同觀點與經驗
- 接受建設性批評
- 關注對社群最有利的事項
- 對其他社群成員展現同理心

### 不可接受的行為

- 使用性別化語言或圖像
- 騷擾或侮辱性言論
- 公開或私下的騷擾
- 未經許可發布他人私人資訊
- 其他不專業或不受歡迎的行為

### 執行

若發現違反行為準則的情況，請聯繫專案維護者。

---

## 問題與支援

- **Bug 回報**：開啟 [Issue](https://github.com/YOUR_USERNAME/Design_system_prompts/issues)
- **功能建議**：開啟 [Issue](https://github.com/YOUR_USERNAME/Design_system_prompts/issues)
- **討論**：使用 [Discussions](https://github.com/YOUR_USERNAME/Design_system_prompts/discussions)

---

## 授權

所有貢獻將使用 MIT License（見 [LICENSE](LICENSE)）。

---

**感謝您的貢獻！🎉**

---

**維護者**：Query/Action/Solo 開發團隊
