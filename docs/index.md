# Scenario ID 文件集

歡迎來到 Scenario ID 文件集！這裡包含所有場景 ID 相關的規範和最佳實踐。

## 📚 文件導覽

### 1. 提案分析

**[PRD User Story 場景 Unique ID 提案分析](./SCENARIO_ID_PROPOSAL.md)**

完整的提案分析，包含：
- 各角色（PM、Designer、RD）的影響分析
- 規範建議與落地流程
- 對自動化產出 TDD 流程的幫助

---

### 2. 痛點分析

**[場景 ID 痛點分析：溝通與文件實作](./SCENARIO_ID_PAIN_POINTS_ANALYSIS.md)**

深入分析兩個核心痛點：
- PM、RD、Designer 溝通時缺乏精確場景識別方式
- 文件實作中使用截圖+文字敘述的問題

---

### 3. 命名規範

**[場景 ID 語義化命名規則](./SCENARIO_ID_NAMING_RULES.md)**

完整的命名規範：
- 命名結構：`{FeaturePrefix}-{UserStory}-{Scene}`
- 命名範例對照表
- 使用場景說明

---

### 4. 解決方案

**[場景 ID 解決方案：優勢與改進方案](./SCENARIO_ID_SOLUTION.md)**

解決方案的詳細說明：
- AI 理解優勢
- 文件實作優勢
- 改進方案架構

---

### 5. 跨 Feature 處理

**[場景 ID 跨 Feature 處理規範](./SCENARIO_ID_CROSS_FEATURE.md)**

跨 Feature 場景的處理方式：
- 不同 Feature 共用場景的處理策略
- 場景統整工具
- 實際應用範例

---

### 6. Codebase 連結

**[場景 ID 與 Codebase 連結規範](./SCENARIO_ID_CODEBASE_LINKING.md)**

如何將場景 ID 與 codebase 連結：
- 程式碼註解標註
- 測試案例標註
- 自動化工具

---

### 7. QA 與測試

**[場景 ID 對 QA 與測試的價值](./SCENARIO_ID_QA_AND_TESTING.md)**

場景 ID 對 QA 和 UI Automation Test 的價值：
- 場景覆蓋率檢查工具使用時機
- QA 價值分析
- UI Automation Test 價值分析

---

## 🚀 快速開始

### 本地預覽

```bash
cd scenario_id_docs
mkdocs serve
```

然後在瀏覽器中開啟 `http://127.0.0.1:8000` 即可預覽。

### 建置網站

```bash
cd scenario_id_docs
mkdocs build
```

---

## 📖 使用建議

1. **初次閱讀**：建議從「提案分析」開始，了解整體架構
2. **命名規範**：參考「命名規範」了解如何命名場景 ID
3. **實作指南**：參考「Codebase 連結」了解如何在程式碼中使用
4. **測試整合**：參考「QA 與測試」了解測試相關的應用

---

## 🔗 相關資源

- [MkDocs 官方文件](https://www.mkdocs.org/)
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
- [Mermaid 語法參考](https://mermaid.js.org/)

---

**最後更新**：2024-12-19

