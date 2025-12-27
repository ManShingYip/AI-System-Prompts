# Query

> **防止 AI 做蠢事：安全可控的三模式 AI 助手框架**
> **Prevent AI from Doing Stupid Things: A Safe and Controllable Three-Mode AI Assistant Framework**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/version-2.0-blue.svg)](./product-en/CHANGELOG.md)

---

## 🌏 Languages / 語言

- **[繁體中文版](./product-zh-TW/)** - Traditional Chinese (完整文檔，含架構圖與範例)
- **[English Version](./product-en/)** - English Documentation (Full docs, diagrams & examples)

---

## 🎯 這是什麼？ What is This?

一個為 GitHub Copilot 設計的系統提示詞框架，讓 AI 助手：

- ✅ **不再自作主張** - 預設只分析、不執行
- ✅ **智能深度分析** - 5 步驟分析法：任務拆解、風險評估、方案比較
- ✅ **記憶系統整合** - 自動追蹤任務、權重管理、學會遺忘
- ✅ **三模式支援** - Query（分析）/ Action（執行）/ Solo（長時自主運行）

A system prompt framework designed for GitHub Copilot that makes AI assistants:

- ✅ **Stop acting on their own** - Default to analysis-only, no execution
- ✅ **Intelligent deep analysis** - 5-step analysis: task breakdown, risk assessment, solution comparison
- ✅ **Memory system integration** - Auto task tracking, weight management, learning to forget
- ✅ **Three-mode support** - Query (analyze) / Action (execute) / Solo (long-running autonomous)

---

## 🌟 核心特色 Key Features

### 1️⃣ 強制詢問機制 Forced Ask-Before-Action

- **Query Mode（預設）** 🔍：AI 只分析、不執行，提供多個方案讓您選擇
- AI analyzes only, doesn't execute, provides multiple solutions for you to choose

### 2️⃣ 智能記憶系統 Intelligent Memory System

```
.copilot-memory/
├── ACTIVE/        # 進行中任務（權重 ≥80%）
├── COMPLETED/     # 已完成任務（權重 50-80%）
└── ARCHIVE/       # 90 天以上（權重 <10%，可遺忘）
```

- 時間衰減演算法：9 個閾值（3h→100%, 6h→95%, 1d→90%...90d→5%）
- Time decay algorithm: 9 thresholds (3h→100%, 6h→95%, 1d→90%...90d→5%)

### 3️⃣ 三模式架構 Three-Mode Architecture

- **Query Mode**：日常分析與建議（預設）
- **Action Mode**：需明確授權詞才執行（如「請執行」「confirm」「go ahead」）
- **Solo Mode**：長時自主運行，定期 checkpoint 回報

### 4️⃣ 架構之旅 Architecture Highlights

- 📊 **決策樹系統**：自動判斷任務複雜度、風險等級、是否需要拆解
- � **風險矩陣**：4x4 評估表（低/中/高/極高 × 影響範圍）
- 🔄 **多任務追蹤**：偵測「和」「還有」「以及」、逗號、編號，自動拆解並追蹤
- 🎨 **視覺化圖表**：架構圖、決策樹、流程圖（drawio 格式）
- 📊 **Decision tree system**: Auto-detects task complexity, risk level, decomposition needs
- 📈 **Risk matrix**: 4x4 assessment (Low/Med/High/Critical × Impact scope)
- 🔄 **Multi-task tracking**: Detects "and", "also", commas, numbering; auto-splits & tracks
- 🎨 **Visual diagrams**: Architecture, decision trees, flow charts (drawio format)

---

## 📁 專案結構 Project Structure

```
product-en/                      # 英文完整文檔 English docs
  .copilot-memory/               # 記憶系統（權重、時間配置、任務追蹤）
    ├── WEIGHT_CONFIG.md         # Memory system (weight, time config, task tracking)
    ├── TIME_CONFIG.md
    ├── ACTIVE/
    ├── COMPLETED/
    └── ARCHIVE/
  .github/
    └── copilot-instructions.md  # 核心系統提示詞 Core system prompt
  diagrams/                      # 架構圖、決策樹、流程圖 (.drawio)
    ├── architecture.drawio      # Architecture, decision tree, flow diagrams
    ├── decision-tree-main.drawio
    ├── multi-task-flow.drawio
    └── test-simple.drawio
  docs/                          # 詳細文檔 Detailed docs
    ├── architecture.md          # 架構設計（569 行）Architecture design (569 lines)
    ├── decision-tree.md         # 決策樹邏輯 Decision tree logic
    ├── risk-matrix.md           # 風險評估矩陣 Risk assessment matrix
    └── use-cases.md             # 實際使用案例 Real use cases
  examples/                      # 實戰範例 Practical examples
    ├── debugging.md             # 除錯示範 Debugging demos
    └── refactoring.md           # 重構示範 Refactoring demos
  integration/                   # 整合指南 Integration guides
    └── vscode.md                # VS Code 整合 VS Code integration
  install-*.bat                  # 三種模式安裝腳本 3 mode installers
  CHANGELOG.md, CONTRIBUTING.md, LICENSE, README.md

product-zh-TW/                   # 繁體中文完整文檔（結構同上）
  ...                            # Traditional Chinese docs (same structure)

README.md                        # 專案總覽（本檔案）Project overview (this file)
```

### 文件亮點 Documentation Highlights

- 📘 **569 行架構文檔**：涵蓋設計理念、技術實現、最佳實踐
- 🎨 **4 個視覺化圖表**：用 drawio 繪製的架構圖與流程圖
- 💡 **實戰範例**：除錯、重構的真實使用情境
- 🛠️ **整合指南**：一鍵安裝腳本，支援 VS Code
- 📘 **569-line architecture doc**: Covers design principles, implementation, best practices
- 🎨 **4 visual diagrams**: Architecture & flow charts in drawio format
- 💡 **Real-world examples**: Debugging & refactoring scenarios
- 🛠️ **Integration guides**: One-click installers for VS Code

---

## ⚡ 快速開始 Quick Start

1. 複製 `.github/copilot-instructions.md` 到您的專案根目錄的 `.github/` 資料夾
   Copy `.github/copilot-instructions.md` to your project's `.github/` folder
2. 複製 `.copilot-memory/` 資料夾到您的專案根目錄
   Copy `.copilot-memory/` folder to your project root
3. 重啟 VS Code / Restart VS Code

---

## 📚 深入了解 Learn More

### 📖 核心文檔 Core Documentation

- [架構設計 Architecture](./product-en/docs/architecture.md) - 569 行深度解析
- [決策樹邏輯 Decision Tree](./product-en/docs/decision-tree.md) - AI 如何判斷執行與否
- [風險矩陣 Risk Matrix](./product-en/docs/risk-matrix.md) - 4x4 風險評估表
- [使用案例 Use Cases](./product-en/docs/use-cases.md) - 實際應用情境

### 🎨 視覺化圖表 Visual Diagrams

- [整體架構圖](./product-en/diagrams/architecture.drawio)
- [決策樹圖](./product-en/diagrams/decision-tree-main.drawio)
- [多任務流程圖](./product-en/diagrams/multi-task-flow.drawio)

### 💡 實戰範例 Examples

- [除錯示範 Debugging](./product-en/examples/debugging.md)
- [重構示範 Refactoring](./product-en/examples/refactoring.md)

### 🤝 參與貢獻 Contributing

- 查看 [CONTRIBUTING.md](./product-en/CONTRIBUTING.md)
- 版本歷史 [CHANGELOG.md](./product-en/CHANGELOG.md)

---

## 🎯 適用情境 Use Cases

✅ **軟體開發團隊**：防止 AI 誤執行破壞性操作（刪檔、覆蓋重要代碼）
✅ **個人專案**：需要 AI 深度分析但不想立即執行
✅ **學習程式設計**：讓 AI 提供多個方案，自己做決策
✅ **長時專案**：利用記憶系統追蹤進度，不會遺忘關鍵任務

✅ **Software dev teams**: Prevent AI from executing destructive operations
✅ **Personal projects**: Need deep AI analysis without immediate execution
✅ **Learning to code**: AI provides multiple solutions, you make decisions
✅ **Long-term projects**: Memory system tracks progress, never forgets key tasks

---

## 📜 授權 License

MIT License - 可自由使用、修改、商業化
MIT License - Free to use, modify, and commercialize

---

**最後更新 Last Updated**：2025-12-28
**版本 Version**：v2.0（統一安全模式 Unified Safe Mode）
