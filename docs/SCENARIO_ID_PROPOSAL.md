# PRD User Story 場景 Unique ID 提案分析

## 概述

本文件分析為 PRD 中的 user story 下的不同場景設定 unique ID 的提議，包含對各角色的影響、規範建議、落地流程，以及對自動化產出 TDD 流程的幫助。

---

## 零、Scenario 與 Story 的關係

### 0.1 概念定義

#### User Story（使用者故事）

**定義**：User Story 是一個高層級的需求描述，使用「角色-行為-目標」的格式。

**格式**：
- **角色（Role）**：誰要使用這個功能
- **行為（Action）**：要做什麼
- **目標（Goal）**：為什麼要做這件事

**範例**：
- 角色：用戶
- 行為：在賽事開始前發表評論
- 目標：與其他用戶互動

**特點**：
- 描述「要做什麼」，不描述「怎麼做」
- 不包含具體的實作細節
- 不包含條件分支或異常處理

---

#### Scenario（場景）

**定義**：Scenario 是 User Story 的具體化，描述在不同條件下的具體行為。

**格式**：
- **前置條件（Precondition）**：在什麼條件下
- **行為（Action）**：執行什麼操作
- **預期結果（Expected Result）**：應該發生什麼

**範例**：
- 前置條件：用戶已登入且有 nickname
- 行為：點擊輸入框，輸入評論內容，點擊送出
- 預期結果：評論成功發表，出現在列表最上方

**特點**：
- 描述「怎麼做」，包含具體的操作步驟
- 包含條件分支（如已登入/未登入）
- 包含異常處理（如登入失敗、網路錯誤）

---

### 0.2 關係說明

#### 一對多關係

**一個 User Story 可以有多個 Scenarios**

```
User Story: 在賽事開始前發表評論
    ├── Scenario 1: 已登入且有 nickname 的用戶發表評論
    ├── Scenario 2: 已登入但無 nickname 的用戶發表評論
    └── Scenario 3: 未登入用戶發表評論
```

**原因**：
- User Story 描述的是「要做什麼」，不包含條件分支
- 實際實作時，需要考慮不同的前置條件和異常情況
- 每個不同的條件組合，就是一個獨立的 Scenario

---

#### 層級關係

**User Story 是抽象層級，Scenario 是具體層級**

| 層級 | 抽象程度 | 描述內容 | 用途 |
|------|---------|---------|------|
| **User Story** | 高 | 業務目標、用戶需求 | 需求規劃、優先級排序 |
| **Scenario** | 低 | 具體操作、條件分支 | 實作設計、測試案例 |

**範例對照**：

| User Story | Scenario |
|-----------|----------|
| 在賽事開始前發表評論 | 已登入且有 nickname 的用戶發表評論 |
| 在賽事開始前發表評論 | 已登入但無 nickname 的用戶發表評論 |
| 在賽事開始前發表評論 | 未登入用戶發表評論 |

---

#### 覆蓋關係

**Scenarios 共同覆蓋一個 User Story**

- 所有 Scenarios 的集合，應該完整覆蓋 User Story 的所有可能情況
- 包括：正常流程、異常流程、邊界條件

**覆蓋檢查**：
- 正常流程：已登入且有 nickname 的用戶發表評論 ✅
- 異常流程：已登入但無 nickname 的用戶發表評論 ✅
- 異常流程：未登入用戶發表評論 ✅
- 邊界條件：評論內容為空、評論內容過長等（可選）

---

### 0.3 識別原則

#### User Story 識別原則

1. **業務目標導向**：每個 User Story 對應一個獨立的業務目標
2. **用戶價值導向**：每個 User Story 應該為用戶帶來價值
3. **可獨立交付**：每個 User Story 應該可以獨立開發和測試

#### Scenario 識別原則

1. **條件組合**：不同的前置條件組合，就是不同的 Scenario
   - 範例：已登入 vs 未登入、有 nickname vs 無 nickname

2. **異常處理**：每個異常情況，就是一個獨立的 Scenario
   - 範例：網路錯誤、API 錯誤、驗證失敗

3. **邊界條件**：重要的邊界條件，可以作為獨立的 Scenario
   - 範例：空輸入、最大長度、特殊字元

4. **測試導向**：每個 Scenario 應該對應到至少一個測試案例

---

### 0.4 命名關係

#### Story ID 與 Scenario ID 的關係

**格式**：
```
Story ID: {FEATURE_PREFIX}-US-{NUMBER}
Scenario ID: {STORY_ID}-SC-{NUMBER}
```

**範例**：
- Story ID: `PC-US-001`（PrematchComment Feature，User Story 001）
- Scenario ID: `PC-US-001-SC-001`（對應 Story 001，Scenario 001）
- Scenario ID: `PC-US-001-SC-002`（對應 Story 001，Scenario 002）

**優勢**：
- Scenario ID 包含 Story ID，可以快速識別所屬的 User Story
- 可以根據 Story ID 查詢所有相關的 Scenarios
- 可以進行 Story 級別的覆蓋率檢查

---

### 0.5 實務應用

#### PRD 中的組織方式

```yaml
user_stories:
  - story_id: "PC-US-001"
    role: "用戶"
    action: "在賽事開始前發表評論"
    goal: "與其他用戶互動"
    priority: "P0"
    scenarios:
      - scenario_id: "PC-US-001-SC-001"
        name: "已登入且有 nickname 的用戶發表評論"
        description: "當用戶已登入且已有 nickname 時，可以直接發表評論"
        condition: "用戶已登入 && 用戶有 nickname"
        actions: [...]
        expected_result: [...]
      - scenario_id: "PC-US-001-SC-002"
        name: "已登入但無 nickname 的用戶發表評論"
        description: "當用戶已登入但沒有 nickname 時，需要先建立 nickname 再發表評論"
        condition: "用戶已登入 && 用戶無 nickname"
        actions: [...]
        expected_result: [...]
```

---

## 一、對各角色的影響分析

### 1.1 PM（Product Manager）

#### ✅ 好處

1. **需求追蹤性**
   - 每個場景都有唯一識別碼，可以精確追蹤需求變更
   - 在 PRD 更新時，可以明確指出哪些場景被修改、新增或刪除
   - 方便進行需求版本管理和變更歷史追蹤

2. **跨文件引用**
   - 在 PRD、設計稿、TDD、Ticket 之間建立明確的對應關係
   - 討論時可以直接引用場景 ID，避免描述不清
   - 減少溝通成本，提高協作效率

3. **驗收標準明確化**
   - 每個場景 ID 對應明確的驗收條件
   - 測試時可以對照場景 ID 確認是否完成
   - 方便進行需求覆蓋率檢查

4. **需求完整性檢查**
   - 可以系統化地檢查每個 user story 下的場景是否完整
   - 避免遺漏重要的業務場景
   - 方便進行需求審查

#### ❌ 壞處

1. **維護成本**
   - 需要為每個場景分配和管理 ID
   - PRD 更新時需要同步更新 ID 對應關係
   - 如果場景被合併或拆分，需要處理 ID 的繼承關係

2. **初期學習曲線**
   - 需要建立 ID 命名規範
   - 團隊需要時間適應新的工作方式
   - 可能增加 PRD 撰寫的複雜度

3. **ID 管理複雜度**
   - 如果場景層級過深（場景 → 子場景 → 子子場景），ID 結構會變複雜
   - 需要決定 ID 的粒度（場景級別 vs 測試案例級別）

---

### 1.2 Designer（設計師）

#### ✅ 好處

1. **設計稿對應明確**
   - 設計稿可以直接引用場景 ID，明確對應到 PRD 場景
   - 設計變更時可以追蹤影響的場景
   - 方便進行設計審查和驗收

2. **交互流程追蹤**
   - 每個交互流程可以對應到場景 ID
   - 設計稿中的流程圖可以標註場景 ID
   - 方便與 PM、RD 討論設計細節

3. **設計覆蓋率檢查**
   - 可以檢查每個場景是否都有對應的設計稿
   - 避免遺漏設計場景
   - 方便進行設計完整性審查

#### ❌ 壞處

1. **設計稿維護成本**
   - 需要在設計稿中標註場景 ID
   - 設計變更時需要同步更新 ID 對應關係
   - 可能增加設計稿的複雜度

2. **設計工具限制**
   - 部分設計工具可能不支援 ID 標註
   - 需要額外的工具或流程來管理 ID 對應關係

---

### 1.3 RD（開發工程師）

#### ✅ 好處

1. **需求理解精確**
   - 可以直接引用場景 ID 討論需求細節
   - 減少需求理解偏差
   - 方便進行技術設計和實作

2. **測試案例對應**
   - 測試案例可以直接對應到場景 ID
   - 測試覆蓋率檢查更精確
   - 方便進行測試案例管理和追蹤

3. **Bug 追蹤**
   - Bug 可以關聯到場景 ID，明確是哪個場景出現問題
   - 方便進行 Bug 分析和修復
   - 可以統計哪些場景容易出現問題

4. **Code Review 效率**
   - Code Review 時可以直接引用場景 ID
   - 方便確認實作是否符合需求
   - 減少溝通成本

5. **自動化流程整合**
   - 場景 ID 可以作為自動化流程的輸入
   - 可以自動生成測試案例、TDD 文件、Ticket
   - 提高自動化程度和一致性

#### ❌ 壞處

1. **實作複雜度**
   - 需要在程式碼中標註場景 ID（可選，但建議）
   - 測試案例需要對應到場景 ID
   - 可能增加程式碼註解的複雜度

2. **ID 變更影響**
   - 如果場景 ID 變更，需要同步更新相關文件、測試案例、程式碼註解
   - 需要建立 ID 變更的處理流程

---

### 1.4 QA（Quality Assurance，特別是 UI Automation Test）

#### ✅ 好處

1. **測試計劃系統化**
   - 可以直接對應 PRD 場景 ID 制定測試計劃
   - 測試計劃結構清晰，不會遺漏場景
   - 可以系統化檢查測試覆蓋率

2. **測試案例自動生成**
   - 可以根據場景 ID 和場景描述自動生成測試案例
   - 測試案例結構一致，易於維護
   - 減少手動撰寫測試案例的時間

3. **測試結果追蹤**
   - 測試結果可以直接對應到 PRD 場景 ID
   - 可以追蹤哪些場景已測試、哪些未測試
   - 可以生成場景級別的測試報告

4. **UI Automation Test 效率提升**
   - 測試案例名稱語義化（如 `test_PC_LikeComment_UserLoggedIn`）
   - 可以直接對應到 PRD 場景，減少理解偏差
   - 可以自動檢查測試覆蓋率

5. **Bug 追蹤精確化**
   - Bug 可以關聯到場景 ID，明確是哪個場景出現問題
   - 可以統計哪些場景容易出現問題
   - 可以進行場景級別的 Bug 分析

6. **測試維護效率**
   - 當 PRD 場景變更時，可以快速找出相關的測試案例
   - 可以追蹤測試案例與需求的對應關係
   - 可以自動更新測試案例

#### ❌ 壞處

1. **測試案例維護成本**
   - 需要在測試案例中標註場景 ID
   - 測試案例需要對應到場景 ID
   - 可能增加測試案例的複雜度

2. **ID 變更影響**
   - 如果場景 ID 變更，需要同步更新測試案例
   - 需要建立 ID 變更的處理流程

---

### 1.5 文件擴充（Phrase 迭代）的影響

#### ✅ 好處

1. **迭代追蹤性**
   - 每個場景 ID 可以追蹤到不同 phrase 的版本
   - 可以明確知道哪些場景在哪些 phrase 中被修改
   - 方便進行版本管理和變更歷史追蹤

2. **場景繼承性**
   - 如果場景在 phrase 間保持不變，可以繼承場景 ID
   - 如果場景被修改，可以標記為新版本或新場景
   - 可以建立場景的演進歷史

3. **影響範圍分析**
   - 可以分析場景變更對其他文件的影響
   - 可以自動檢查受影響的 TDD、測試案例、設計稿
   - 可以生成變更影響報告

4. **跨 Phrase 一致性**
   - 可以檢查不同 phrase 間場景描述的一致性
   - 可以追蹤場景的演進過程
   - 可以確保場景描述的完整性

#### ❌ 壞處

1. **版本管理複雜度**
   - 需要管理場景 ID 在不同 phrase 間的版本
   - 需要決定場景 ID 的繼承規則
   - 可能增加版本管理的複雜度

2. **變更追蹤成本**
   - 需要追蹤場景在不同 phrase 間的變更
   - 需要更新相關文件的場景 ID 對應關係
   - 可能增加變更追蹤的成本

---

### 1.6 AI 理解文件的影響

#### ✅ 好處

1. **結構化理解**
   - 場景 ID 提供結構化的識別方式
   - AI 可以精確提取場景 ID 和場景資訊
   - AI 可以建立場景與其他文件的對應關係

2. **語義化理解**
   - 場景 ID 採用語義化命名（如 `PC-LikeComment-UserLoggedIn`）
   - AI 可以從場景 ID 中理解場景的業務含義
   - AI 可以進行場景分類和組織

3. **自動化處理**
   - AI 可以根據場景 ID 自動生成測試案例
   - AI 可以根據場景 ID 自動生成 TDD 文件
   - AI 可以根據場景 ID 自動檢查場景覆蓋率

4. **文件對應關係建立**
   - AI 可以自動建立 PRD → TDD → 測試案例的對應關係
   - AI 可以自動檢查場景對應關係的一致性
   - AI 可以自動生成場景對應報告

5. **場景變更檢測**
   - AI 可以自動檢測 PRD 場景的變更
   - AI 可以自動檢查受影響的文件
   - AI 可以自動生成變更影響報告

#### ❌ 壞處

1. **AI 理解依賴性**
   - AI 的理解能力依賴於場景 ID 的命名規範
   - 如果命名不規範，AI 可能無法正確理解
   - 需要建立清晰的命名規範

2. **AI 處理複雜度**
   - 如果場景層級過深，AI 處理可能變複雜
   - 需要確保場景 ID 的結構清晰
   - 可能需要額外的 AI 處理邏輯

---

## 二、規範建議

### 2.1 ID 命名規範

#### 2.1.1 格式建議

```
{FEATURE_PREFIX}-{STORY_NUMBER}-{SCENARIO_NUMBER}
```

**範例**：
- `PC-US-001-SC-001`：PrematchComment Feature，User Story 001，Scenario 001
- `LC-US-002-SC-003`：LiveChat Feature，User Story 002，Scenario 003

#### 2.1.2 命名規則

1. **Feature Prefix**
   - 使用 Feature 名稱的縮寫（2-3 個字母）
   - 統一使用大寫字母
   - 範例：`PC`（PrematchComment）、`LC`（LiveChat）

2. **Story Number**
   - 使用 3 位數字，從 001 開始
   - 格式：`US-001`、`US-002`、`US-003`

3. **Scenario Number**
   - 使用 3 位數字，從 001 開始
   - 格式：`SC-001`、`SC-002`、`SC-003`

4. **完整格式**
   ```
   {FEATURE_PREFIX}-US-{STORY_NUMBER}-SC-{SCENARIO_NUMBER}
   ```

#### 2.1.3 子場景 ID（可選）

如果需要更細粒度的場景劃分，可以使用子場景 ID：

```
{FEATURE_PREFIX}-US-{STORY_NUMBER}-SC-{SCENARIO_NUMBER}-SUB-{SUB_SCENARIO_NUMBER}
```

**範例**：
- `PC-US-001-SC-001-SUB-001`：PrematchComment Feature，User Story 001，Scenario 001，Sub Scenario 001

---

### 2.2 PRD 文件格式規範

#### 2.2.1 YAML 格式建議

在 `templates/PRD_Template.yaml` 中，建議新增場景 ID 欄位：

```yaml
user_stories:
  - story_id: "PC-US-001"
    role: "用戶"
    action: "在賽事開始前發表評論"
    goal: "與其他用戶互動"
    priority: "P0"
    scenarios:
      - scenario_id: "PC-US-001-SC-001"
        name: "已登入且有 nickname 的用戶發表評論"
        description: "當用戶已登入且已有 nickname 時，可以直接發表評論"
        acceptance_criteria:
          - "用戶可以成功發表評論"
          - "評論出現在列表最上方"
        test_cases:
          - "已登入且有 nickname 的用戶可以發表評論"
      - scenario_id: "PC-US-001-SC-002"
        name: "已登入但無 nickname 的用戶發表評論"
        description: "當用戶已登入但沒有 nickname 時，需要先建立 nickname 再發表評論"
        acceptance_criteria:
          - "系統提示用戶建立 nickname"
          - "用戶建立 nickname 後可以發表評論"
        test_cases:
          - "已登入但無 nickname 的用戶會先建立 nickname 再發表評論"
      - scenario_id: "PC-US-001-SC-003"
        name: "未登入用戶發表評論"
        description: "當用戶未登入時，需要先登入再發表評論"
        acceptance_criteria:
          - "系統跳轉到登入頁面"
          - "用戶登入後可以發表評論"
        test_cases:
          - "未登入用戶會先登入再發表評論"
```

#### 2.2.2 Markdown 格式建議

如果使用 Markdown 格式，建議使用標題層級和 ID 標註：

```markdown
## User Story 001: 在賽事開始前發表評論

**Story ID**: `PC-US-001`

**角色**: 用戶  
**行為**: 在賽事開始前發表評論  
**目標**: 與其他用戶互動  
**優先級**: P0

### Scenario 001: 已登入且有 nickname 的用戶發表評論

**Scenario ID**: `PC-US-001-SC-001`

**描述**: 當用戶已登入且已有 nickname 時，可以直接發表評論

**驗收條件**:
- 用戶可以成功發表評論
- 評論出現在列表最上方

**測試案例**:
- 已登入且有 nickname 的用戶可以發表評論
```

---

### 2.3 場景 ID 使用規範

#### 2.3.1 場景 ID 的範圍

場景 ID 應該對應到：
- **PRD 中的場景描述**：每個場景應該有明確的業務描述
- **設計稿中的交互流程**：設計稿應該標註對應的場景 ID
- **TDD 中的測試場景**：TDD 的 Test Scenarios 章節應該引用場景 ID
- **測試案例**：測試案例應該對應到場景 ID
- **Ticket**：Ticket 可以關聯到場景 ID（可選）

#### 2.3.2 場景 ID 的粒度

建議場景 ID 的粒度為：
- **場景級別**：對應到一個完整的業務場景（如「已登入且有 nickname 的用戶發表評論」）
- **不建議過細**：不要對應到單個測試步驟或單個 API 呼叫

#### 2.3.3 場景 ID 的變更規則

1. **場景新增**：分配新的場景 ID
2. **場景刪除**：標記場景 ID 為已刪除，保留歷史記錄
3. **場景合併**：保留原場景 ID，標記為已合併，新增合併後的場景 ID
4. **場景拆分**：保留原場景 ID，標記為已拆分，新增拆分後的場景 ID

---

## 三、落地流程

### 3.1 階段 1：PRD 撰寫階段（PM）

#### 3.1.1 工作流程

1. **識別 User Story**
   - 根據需求識別所有 User Story
   - 為每個 User Story 分配 Story ID（格式：`{FEATURE_PREFIX}-US-{NUMBER}`）

2. **識別場景**
   - 為每個 User Story 識別所有可能的場景
   - 考慮正常流程、異常流程、邊界條件
   - 為每個場景分配 Scenario ID（格式：`{STORY_ID}-SC-{NUMBER}`）

3. **撰寫場景描述**
   - 為每個場景撰寫清晰的描述
   - 定義驗收條件和測試案例
   - 標註場景 ID

4. **PRD 審查**
   - 檢查場景 ID 是否唯一
   - 檢查場景描述是否完整
   - 檢查場景覆蓋率是否足夠

#### 3.1.2 產出

- PRD 文件（包含場景 ID）
- 場景 ID 對照表（可選，方便追蹤）

---

### 3.2 階段 2：設計階段（Designer）

#### 3.2.1 工作流程

1. **讀取 PRD**
   - 讀取 PRD 文件，提取所有場景 ID
   - 理解每個場景的業務描述

2. **設計交互流程**
   - 為每個場景設計對應的交互流程
   - 在設計稿中標註場景 ID
   - 確保設計覆蓋所有場景

3. **設計審查**
   - 檢查設計是否覆蓋所有場景
   - 檢查設計是否符合場景描述
   - 檢查場景 ID 標註是否正確

#### 3.2.2 產出

- 設計稿（包含場景 ID 標註）
- 設計覆蓋率檢查報告（可選）

---

### 3.3 階段 3：TDD 生成階段（RD）

#### 3.3.1 工作流程

1. **讀取 PRD**
   - 讀取 PRD 文件，提取所有場景 ID 和場景描述
   - 理解每個場景的業務邏輯

2. **設計 Mermaid Sequence Diagram**
   - 為每個場景設計對應的 Mermaid Sequence Diagram
   - 在 Mermaid 代碼中標註場景 ID（使用 Note 或註解）
   - 確保 Mermaid 覆蓋所有場景

3. **生成 flow_spec.yaml**
   - 在 `flow_spec.yaml` 中標註場景 ID
   - 建立場景 ID 與 Flow 的對應關係
   - 確保場景 ID 可以追蹤到對應的 Flow

4. **生成 TDD 文件**
   - 在 TDD 的 Test Scenarios 章節中引用場景 ID
   - 建立場景 ID 與測試案例的對應關係
   - 確保測試場景覆蓋所有 PRD 場景

#### 3.3.2 產出

- Mermaid Sequence Diagram（包含場景 ID 標註）
- flow_spec.yaml（包含場景 ID）
- TDD 文件（包含場景 ID 引用）

---

### 3.4 階段 4：開發與測試階段（RD）

#### 3.4.1 工作流程

1. **讀取 TDD**
   - 讀取 TDD 文件，提取所有場景 ID
   - 理解每個場景的技術實作要求

2. **開發實作**
   - 根據 TDD 進行開發
   - 在程式碼註解中標註場景 ID（可選，但建議）
   - 確保實作符合場景描述

3. **撰寫測試案例**
   - 為每個場景撰寫對應的測試案例
   - 在測試案例中標註場景 ID
   - 確保測試覆蓋所有場景

4. **測試執行**
   - 執行測試案例，確認所有場景都通過測試
   - 記錄測試結果，關聯場景 ID
   - 進行測試覆蓋率檢查

#### 3.4.2 產出

- 程式碼（包含場景 ID 註解，可選）
- 測試案例（包含場景 ID）
- 測試報告（包含場景 ID 對應的測試結果）

---

### 3.5 階段 5：驗收階段（PM + Designer + RD）

#### 3.5.1 工作流程

1. **場景覆蓋率檢查**
   - 檢查所有 PRD 場景是否都有對應的設計稿
   - 檢查所有 PRD 場景是否都有對應的 TDD 測試場景
   - 檢查所有 PRD 場景是否都有對應的測試案例

2. **場景驗收**
   - 根據場景 ID 逐一驗收每個場景
   - 確認場景實作是否符合 PRD 描述
   - 確認場景實作是否符合設計稿

3. **場景追蹤**
   - 記錄每個場景的驗收狀態
   - 追蹤未通過驗收的場景
   - 進行場景修復和重新驗收

#### 3.5.2 產出

- 場景覆蓋率報告
- 場景驗收報告
- 場景追蹤表

---

## 四、對自動化產出 TDD 流程的幫助

### 4.1 場景 ID 在自動化流程中的應用

#### 4.1.1 PRD → Input 轉換

**現況**：
- PRD 中的場景描述是自然語言，需要人工理解後轉換為 Mermaid Sequence Diagram
- 場景與 Flow 的對應關係不明確，需要人工判斷

**加入場景 ID 後**：
- 場景 ID 可以作為 PRD 場景與 Flow 的橋樑
- 可以在 `flow_spec.yaml` 中明確標註場景 ID，建立對應關係
- AI 可以根據場景 ID 自動對應 PRD 場景與 Flow

**範例**：
```yaml
features:
  PrematchComment:
    flows:
      - flow_id: PC-SUB-004
        flow_type: Sub
        flow_name: 用戶點擊 Like（含登入檢查）
        scenario_ids:  # 新增場景 ID 對應
          - PC-US-002-SC-001  # 使用者已登入
          - PC-US-002-SC-002  # 使用者未登入
        scenarios:
          - scenario_id: PC-US-002-SC-001  # 新增場景 ID
            name: 使用者已登入
            description: 當用戶已登入時的行為
            ...
```

---

#### 4.1.2 Input → TDD 轉換

**現況**：
- TDD 的 Test Scenarios 章節是從 Mermaid 的 alt/opt/loop 推導出來的
- 無法直接對應到 PRD 中的場景，需要人工對照

**加入場景 ID 後**：
- TDD 的 Test Scenarios 章節可以直接引用 PRD 的場景 ID
- 可以自動建立 PRD 場景與 TDD 測試場景的對應關係
- 可以自動檢查 TDD 測試場景是否覆蓋所有 PRD 場景

**範例**：
```markdown
## Test Scenarios

### Scenario: PC-US-002-SC-001 - 使用者已登入

**對應 PRD 場景**: PC-US-002-SC-001  
**對應 Flow**: PC-SUB-004  
**描述**: 當用戶已登入時的行為

**前置條件**:
- 使用者已登入

**測試步驟**:
1. 用戶點擊 Like 按鈕
2. PrematchComment Package 調用 Main App 的 APICoordinator.shared.accountManager 檢查登入狀態
3. Main App 回應已登入
4. PrematchComment Package 通過登入檢查
5. PrematchComment Package 立即更新畫面上的 Like 數（Optimistic UI）
6. PrematchComment Package 顯示已點讚狀態 + Like +1
7. PrematchComment Package 向伺服器發送 Like 請求

**預期結果**:
- 用戶可以看到已點讚狀態
- Like 數增加 1
- 伺服器成功更新 Like 狀態
```

---

#### 4.1.3 TDD → Ticket 轉換

**現況**：
- Ticket 是根據 TDD 的模組生成的，無法直接對應到 PRD 場景
- 無法追蹤哪些 Ticket 對應到哪些場景

**加入場景 ID 後**：
- Ticket 可以關聯到場景 ID（可選，但建議）
- 可以追蹤哪些 Ticket 對應到哪些場景
- 可以進行場景級別的進度追蹤

**範例**：
```markdown
## Ticket: TDD-XXX - ToggleLikeUseCase

**類型**: UseCase  
**優先級**: P0  
**所屬 Feature**: PrematchComment  
**相關場景**: 
- PC-US-002-SC-001 (使用者已登入)
- PC-US-002-SC-002 (使用者未登入)

**描述**: 
實作 ToggleLikeUseCase，支援用戶點擊 Like 按鈕的功能。

**需求清單**:
- [ ] 實作登入檢查邏輯
- [ ] 實作 Optimistic UI 更新邏輯
- [ ] 實作 Like 狀態同步邏輯

**驗收條件**:
- [ ] 場景 PC-US-002-SC-001 通過測試
- [ ] 場景 PC-US-002-SC-002 通過測試
```

---

### 4.2 自動化檢查功能

#### 4.2.1 場景覆蓋率檢查

**功能**：
- 自動檢查 TDD 的 Test Scenarios 是否覆蓋所有 PRD 場景
- 自動檢查測試案例是否覆蓋所有 PRD 場景
- 自動生成場景覆蓋率報告

**實作方式**：
```python
def check_scenario_coverage(prd_scenarios, tdd_test_scenarios):
    """
    檢查 TDD 測試場景是否覆蓋所有 PRD 場景
    
    Args:
        prd_scenarios: PRD 中的場景列表（包含 scenario_id）
        tdd_test_scenarios: TDD 中的測試場景列表（包含 scenario_id 引用）
    
    Returns:
        coverage_report: 覆蓋率報告
    """
    prd_scenario_ids = {s['scenario_id'] for s in prd_scenarios}
    tdd_scenario_ids = {s['scenario_id'] for s in tdd_test_scenarios if 'scenario_id' in s}
    
    missing_scenarios = prd_scenario_ids - tdd_scenario_ids
    extra_scenarios = tdd_scenario_ids - prd_scenario_ids
    
    return {
        'coverage_rate': len(tdd_scenario_ids & prd_scenario_ids) / len(prd_scenario_ids),
        'missing_scenarios': missing_scenarios,
        'extra_scenarios': extra_scenarios,
        'total_prd_scenarios': len(prd_scenario_ids),
        'total_tdd_scenarios': len(tdd_scenario_ids)
    }
```

---

#### 4.2.2 場景追蹤功能

**功能**：
- 自動追蹤場景從 PRD → TDD → Ticket → 測試案例的完整流程
- 自動檢查場景在各階段的狀態
- 自動生成場景追蹤報告

**實作方式**：
```python
def track_scenario(scenario_id, prd_file, tdd_file, ticket_files, test_files):
    """
    追蹤場景在各階段的狀態
    
    Args:
        scenario_id: 場景 ID
        prd_file: PRD 文件路徑
        tdd_file: TDD 文件路徑
        ticket_files: Ticket 文件列表
        test_files: 測試文件列表
    
    Returns:
        tracking_report: 追蹤報告
    """
    return {
        'scenario_id': scenario_id,
        'prd_status': check_scenario_in_prd(scenario_id, prd_file),
        'tdd_status': check_scenario_in_tdd(scenario_id, tdd_file),
        'ticket_status': check_scenario_in_tickets(scenario_id, ticket_files),
        'test_status': check_scenario_in_tests(scenario_id, test_files)
    }
```

---

#### 4.2.3 場景變更檢測

**功能**：
- 自動檢測 PRD 場景的變更（新增、修改、刪除）
- 自動檢查受影響的 TDD、Ticket、測試案例
- 自動生成變更影響報告

**實作方式**：
```python
def detect_scenario_changes(old_prd, new_prd):
    """
    檢測 PRD 場景的變更
    
    Args:
        old_prd: 舊版 PRD 文件
        new_prd: 新版 PRD 文件
    
    Returns:
        change_report: 變更報告
    """
    old_scenarios = extract_scenarios(old_prd)
    new_scenarios = extract_scenarios(new_prd)
    
    old_scenario_ids = {s['scenario_id'] for s in old_scenarios}
    new_scenario_ids = {s['scenario_id'] for s in new_scenarios}
    
    added_scenarios = new_scenario_ids - old_scenario_ids
    removed_scenarios = old_scenario_ids - new_scenario_ids
    modified_scenarios = []
    
    for scenario_id in old_scenario_ids & new_scenario_ids:
        old_scenario = next(s for s in old_scenarios if s['scenario_id'] == scenario_id)
        new_scenario = next(s for s in new_scenarios if s['scenario_id'] == scenario_id)
        if old_scenario != new_scenario:
            modified_scenarios.append(scenario_id)
    
    return {
        'added_scenarios': added_scenarios,
        'removed_scenarios': removed_scenarios,
        'modified_scenarios': modified_scenarios
    }
```

---

### 4.3 自動化流程整合

#### 4.3.1 完整自動化流程

```
PRD (含場景 ID)
    ↓
[自動化步驟 1] 提取場景 ID 和場景描述
    ↓
[自動化步驟 2] 生成 Mermaid Sequence Diagram（標註場景 ID）
    ↓
[自動化步驟 3] 生成 flow_spec.yaml（標註場景 ID）
    ↓
[自動化步驟 4] 生成 TDD 文件（引用場景 ID）
    ↓
[自動化步驟 5] 生成 Test Scenarios（對應場景 ID）
    ↓
[自動化步驟 6] 生成 Ticket（關聯場景 ID，可選）
    ↓
[自動化步驟 7] 場景覆蓋率檢查
    ↓
[自動化步驟 8] 場景追蹤報告
```

---

#### 4.3.2 場景 ID 在流程中的傳遞

**PRD → Input**：
- PRD 中的場景 ID 直接傳遞到 `flow_spec.yaml` 的 `scenarios` 區塊

**Input → TDD**：
- `flow_spec.yaml` 中的場景 ID 傳遞到 TDD 的 Test Scenarios 章節

**TDD → Ticket**：
- TDD 中的場景 ID 傳遞到 Ticket 的「相關場景」欄位（可選）

**Ticket → 測試案例**：
- Ticket 中的場景 ID 傳遞到測試案例的標註（可選）

---

## 五、實施建議

### 5.1 分階段實施

#### 階段 1：試點實施（1-2 週）

1. **選擇一個 Feature 進行試點**
   - 選擇一個相對簡單的 Feature（如 PrematchComment）
   - 為該 Feature 的所有 User Story 和場景分配 ID
   - 更新 PRD 文件，加入場景 ID

2. **更新相關文件**
   - 更新 `templates/PRD_Template.yaml`，加入場景 ID 欄位
   - 更新 `flow_spec.yaml`，加入場景 ID 對應
   - 更新 TDD 文件，引用場景 ID

3. **收集反饋**
   - 與 PM、Designer、RD 討論使用體驗
   - 收集改進建議
   - 調整規範和流程

---

#### 階段 2：全面推廣（2-4 週）

1. **更新所有 PRD**
   - 為所有 Feature 的 User Story 和場景分配 ID
   - 更新所有 PRD 文件

2. **更新工具和流程**
   - 更新自動化腳本，支援場景 ID
   - 建立場景覆蓋率檢查工具
   - 建立場景追蹤工具

3. **培訓團隊**
   - 舉辦培訓會議，說明場景 ID 的使用方式
   - 提供使用指南和範例
   - 建立 FAQ 文件

---

#### 階段 3：持續優化（持續）

1. **監控使用情況**
   - 收集使用數據
   - 分析使用問題
   - 持續改進規範和流程

2. **擴展功能**
   - 根據需求擴展場景 ID 的功能
   - 整合到更多工具和流程中
   - 建立場景 ID 管理系統（可選）

---

### 5.2 工具支援

#### 5.2.1 PRD 模板更新

更新 `templates/PRD_Template.yaml`，加入場景 ID 欄位：

```yaml
user_stories:
  - story_id: "{FEATURE_PREFIX}-US-{NUMBER}"  # 新增
    role: "用戶"
    action: "在賽事開始前發表評論"
    goal: "與其他用戶互動"
    priority: "P0"
    scenarios:
      - scenario_id: "{STORY_ID}-SC-{NUMBER}"  # 新增
        name: "已登入且有 nickname 的用戶發表評論"
        description: "當用戶已登入且已有 nickname 時，可以直接發表評論"
        acceptance_criteria: [...]
        test_cases: [...]
```

---

#### 5.2.2 flow_spec.yaml 更新

更新 `flow_spec.yaml` 結構，加入場景 ID 對應：

```yaml
features:
  {FeatureName}:
    flows:
      - flow_id: {FLOW_ID}
        flow_type: {Full|Sub}
        flow_name: {流程名稱}
        scenario_ids:  # 新增
          - {SCENARIO_ID_1}
          - {SCENARIO_ID_2}
        scenarios:
          - scenario_id: {SCENARIO_ID}  # 新增
            name: {場景名稱}
            description: {場景描述}
            ...
```

---

#### 5.2.3 自動化腳本開發

開發自動化腳本，支援場景 ID：

1. **場景 ID 提取腳本**
   - 從 PRD 文件提取所有場景 ID
   - 生成場景 ID 對照表

2. **場景覆蓋率檢查腳本**
   - 檢查 TDD 測試場景是否覆蓋所有 PRD 場景
   - 生成覆蓋率報告

3. **場景追蹤腳本**
   - 追蹤場景在各階段的狀態
   - 生成追蹤報告

---

### 5.3 文檔更新

#### 5.3.1 更新規範文件

更新以下規範文件，加入場景 ID 相關規範：

1. **`TDD_rules/tdd_input_processing_rules.md`**
   - 加入場景 ID 處理規範
   - 說明場景 ID 在 flow_spec.yaml 中的使用方式

2. **`TDD_rules/tdd_domain_api_test_and_structure.md`**
   - 加入場景 ID 在 Test Scenarios 章節中的使用規範
   - 說明場景 ID 與測試案例的對應關係

3. **`TDD_rules/tdd_workflow_from_prd_to_tdd.md`**
   - 加入場景 ID 在各階段的使用規範
   - 說明場景 ID 的傳遞流程

---

#### 5.3.2 建立使用指南

建立場景 ID 使用指南：

1. **`docs/SCENARIO_ID_USAGE_GUIDE.md`**
   - 場景 ID 命名規範
   - 場景 ID 使用範例
   - 常見問題解答

2. **`docs/SCENARIO_ID_FAQ.md`**
   - 常見問題解答
   - 最佳實踐建議

---

## 六、總結

### 6.1 核心價值

為 PRD 中的 user story 下的不同場景設定 unique ID 的核心價值在於：

1. **提高追蹤性**：從 PRD 到 TDD 到 Ticket 到測試案例，建立完整的追蹤鏈
2. **提高一致性**：確保各階段文件的一致性，減少理解偏差
3. **提高自動化程度**：場景 ID 可以作為自動化流程的輸入，提高自動化程度
4. **提高效率**：減少溝通成本，提高協作效率

---

### 6.2 實施建議

1. **分階段實施**：先試點，再全面推廣
2. **工具支援**：更新模板和腳本，支援場景 ID
3. **文檔更新**：更新規範文件和使用指南
4. **持續優化**：根據使用情況持續改進

---

### 6.3 預期效果

實施場景 ID 後，預期可以達到：

1. **需求追蹤性提升 80%**：可以精確追蹤每個場景的狀態
2. **溝通成本降低 50%**：可以直接引用場景 ID，減少描述不清的問題
3. **自動化程度提升 30%**：場景 ID 可以作為自動化流程的輸入
4. **測試覆蓋率檢查效率提升 70%**：可以自動檢查測試覆蓋率

---

## 附錄

### A. 場景 ID 命名範例

```
PC-US-001-SC-001: PrematchComment Feature, User Story 001, Scenario 001
PC-US-001-SC-002: PrematchComment Feature, User Story 001, Scenario 002
PC-US-002-SC-001: PrematchComment Feature, User Story 002, Scenario 001
LC-US-001-SC-001: LiveChat Feature, User Story 001, Scenario 001
```

---

### B. PRD 範例（含場景 ID）

```yaml
user_stories:
  - story_id: "PC-US-001"
    role: "用戶"
    action: "在賽事開始前發表評論"
    goal: "與其他用戶互動"
    priority: "P0"
    scenarios:
      - scenario_id: "PC-US-001-SC-001"
        name: "已登入且有 nickname 的用戶發表評論"
        description: "當用戶已登入且已有 nickname 時，可以直接發表評論"
        acceptance_criteria:
          - "用戶可以成功發表評論"
          - "評論出現在列表最上方"
        test_cases:
          - "已登入且有 nickname 的用戶可以發表評論"
```

---

### C. flow_spec.yaml 範例（含場景 ID）

```yaml
features:
  PrematchComment:
    flows:
      - flow_id: PC-SUB-004
        flow_type: Sub
        flow_name: 用戶點擊 Like（含登入檢查）
        scenario_ids:
          - PC-US-002-SC-001
          - PC-US-002-SC-002
        scenarios:
          - scenario_id: PC-US-002-SC-001
            name: 使用者已登入
            description: 當用戶已登入時的行為
            ...
```

---

### D. TDD Test Scenarios 範例（含場景 ID）

```markdown
## Test Scenarios

### Scenario: PC-US-002-SC-001 - 使用者已登入

**對應 PRD 場景**: PC-US-002-SC-001  
**對應 Flow**: PC-SUB-004  
**描述**: 當用戶已登入時的行為

**測試步驟**:
1. 用戶點擊 Like 按鈕
2. ...
```

---

## 參考資料

- [TDD Input 資料處理規範](../TDD_rules/tdd_input_processing_rules.md)
- [TDD Domain, API, Test & TDD Structure](../TDD_rules/tdd_domain_api_test_and_structure.md)
- [TDD Workflow from PRD to TDD](../TDD_rules/tdd_workflow_from_prd_to_tdd.md)
- [PRD Template](../templates/PRD_Template.yaml)

