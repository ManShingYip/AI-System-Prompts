# Changelog

All notable changes to the Query/Action/Solo Framework will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.1.0] - 2025-12-17

### Added

#### Memory System（記憶系統）
- **`.copilot-memory/` 記憶系統**：減輕上下文壓力，避免 AI 記憶錯亂
  - 時間衰減原則：越舊的記憶可靠性越低（3h 到 90d+ 的細緻閾值）
  - 白名單機制：只讀取記憶系統內的檔案，避免偷看過時文檔
  - AI 透明化：所有操作都會通知用戶（包含檔案來源、時間、可靠性）
  - 可自定義時間閾值：支援不同開發模式（高強度/標準/維護）

#### Memory System Files
- **`.copilot-memory/README.md`**：完整的記憶系統使用說明
- **`.copilot-memory/PROJECT_OVERVIEW.md`**：專案概覽範本
- **`.copilot-memory/TIME_CONFIG.md`**：時間配置檔案（可自定義）
- **`.copilot-memory/TASKS/`**：任務記錄資料夾
- **`.copilot-memory/DECISIONS/`**：決策記錄資料夾

#### Enhanced Notification System
- **完整通知格式**：包含檔案來源、時間戳記、可靠性百分比、內容摘要
- **多檔案優先級排序**：找到多個相關檔案時，依時間和可靠性排序
- **衝突檢測**：自動檢測記憶檔案內容衝突並通知用戶
- **視覺化指示**：使用 emoji（⭐⚠️📖🔧等）提升可讀性

### Enhanced

#### Time System
- **細緻的時間閾值**：從原本的 7/30/90 天改為 9 個級別
  - 超短期：3 小時 / 6 小時（支援高強度開發）
  - 短期：1 天 / 3 天（Sprint 週期）
  - 中期：7 天 / 16 天（標準專案）
  - 長期：30 / 60 / 90 天（月度/季度）
  - 歷史：90 天以上（給新接手的人參考）

#### System Prompts
- **更新所有系統提示詞**：整合記憶系統規則
  - `prompts/query-only.md`：加入記憶系統和通知規則
  - `prompts/query-action.md`：加入記憶系統和通知規則
  - `copilot-instructions.md`：更新時間系統和通知格式

#### Documentation
- **README.md 新增記憶系統章節**：完整的使用說明和最佳實踐
- **CHANGELOG.md 更新**：記錄所有新功能

### Removed
- **SimpleMem 系統**：移除未經討論的實驗性功能
  - 刪除 `product/simplemem/` 資料夾
  - 刪除 `product/install-simplemem.bat`

---

## [1.0.0] - 2025-12-17

### Added

#### Core Framework
- **Query/Action/Solo 三模式框架**：安全可控的 AI 助手行為規範
  - Query Mode（查詢模式）：只提供建議，不執行變更
  - Action Mode（執行模式）：需授權詞觸發，包含二次確認機制
  - Solo Mode（長時運行模式）：支援多步驟任務與 checkpoint 機制

#### Documentation
- **完整文檔體系**：16 個核心文檔檔案
  - 專案總覽（README.md）
  - 架構設計（docs/architecture.md）
  - 決策樹（docs/decision-tree.md）
  - 風險矩陣（docs/risk-matrix.md）
  - 使用案例（docs/use-cases.md）
  - 系統提示詞範本（prompts/*.md）
  - 整合指南（integration/vscode.md）
  - 貢獻指南（CONTRIBUTING.md）

#### Visual Assets
- **Draw.io 專業流程圖**：3 個可編輯流程圖
  - 多任務處理流程（diagrams/multi-task-flow.drawio）
  - 系統架構圖（diagrams/architecture.drawio）
  - 主決策樹（diagrams/decision-tree-main.drawio）
  - PNG 匯出版本與使用說明

#### Safety Features
- **A/B/C 風險分級體系**：三級風險管理機制
  - 等級 A（高風險）：需二次確認
  - 等級 B（中風險）：需單次確認
  - 等級 C（低風險）：可直接執行
- **授權詞機制**：防止意外執行
- **Checkpoint 系統**：長時運行任務的安全機制

#### Integration
- **VS Code/GitHub Copilot 整合**：完整的整合指南
  - copilot-instructions.md 配置說明
  - 安裝步驟與驗證方法
  - 常見錯誤排除指南

#### Language Support
- **雙語支援**：繁體中文（主要）+ 英文（技術術語）

#### License
- **MIT License + Attribution Notice**：免費開源，商業使用需註明出處

---

## [Unreleased]

### Planned Features
- SQL + NoSQL 混合記憶系統
- AI 原生操作系統（AI OS）基礎設施
- 自然語言查詢介面
- 多 AI Agent 協作機制

---

## Version History

### v1.0.0 (2025-12-17)
- 🎉 首個正式版本發布
- ✅ 核心框架完整
- ✅ 文檔體系完善
- ✅ 安全機制健全
- ✅ 整合指南齊全

---

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

**Attribution Notice**: If you use this framework in commercial products, please include attribution to the Query/Action/Solo Framework.

---

**Last Updated**: 2025-12-17  
**Maintained by**: Open Source Community
