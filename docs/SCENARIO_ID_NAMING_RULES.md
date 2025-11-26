# 場景 ID 語義化命名規則

## 概述

本文件定義場景 ID 的語義化命名規則，讓場景 ID 更具可讀性和語義性，便於團隊成員快速理解場景的業務含義。

---

## 命名結構

場景 ID 採用三層結構：

```
{Feature} - {UserStory} - {Scene}
```

- **Feature**：功能模組名稱（語義化）
- **UserStory**：使用者故事名稱（語義化）
- **Scene**：場景名稱（語義化）

---

## 方案比較

### 方案 1：完全語義化（推薦）

**格式**：`{FeatureName}-{UserStoryName}-{SceneName}`

**範例**：
- `PrematchComment-PublishComment-UserLoggedInWithNickname`
- `PrematchComment-LikeComment-UserLoggedIn`
- `PrematchComment-LikeComment-UserNotLoggedIn`
- `LiveChat-SendMessage-UserLoggedInWithNickname`

**優點**：
- ✅ 語義最清晰，一看就懂
- ✅ 不需要查對照表
- ✅ 適合在文件、討論中直接使用

**缺點**：
- ❌ ID 較長（30-50 字元）
- ❌ 輸入時需要完整輸入

**適用場景**：
- 文件標註
- 討論引用
- 自動化處理

---

### 方案 2：混合方案（平衡）

**格式**：`{FeaturePrefix}-{UserStoryName}-{SceneName}`

**範例**：
- `PC-PublishComment-UserLoggedInWithNickname`
- `PC-LikeComment-UserLoggedIn`
- `PC-LikeComment-UserNotLoggedIn`
- `LC-SendMessage-UserLoggedInWithNickname`

**優點**：
- ✅ Feature 使用縮寫，減少長度
- ✅ UserStory 和 Scene 保持語義化
- ✅ 長度適中（25-40 字元）

**缺點**：
- ❌ 需要記住 Feature 縮寫對照表

**適用場景**：
- 程式碼註解
- 測試案例命名
- 文件標註

---

### 方案 3：分層語義（使用分隔符）

**格式**：`{FeatureName}/{UserStoryName}/{SceneName}`

**範例**：
- `PrematchComment/PublishComment/UserLoggedInWithNickname`
- `PrematchComment/LikeComment/UserLoggedIn`
- `PrematchComment/LikeComment/UserNotLoggedIn`
- `LiveChat/SendMessage/UserLoggedInWithNickname`

**優點**：
- ✅ 分層清晰，易於理解
- ✅ 可以使用路徑式引用
- ✅ 適合檔案系統組織

**缺點**：
- ❌ 在某些系統中 `/` 可能有特殊意義
- ❌ 長度較長

**適用場景**：
- 檔案命名
- 目錄結構
- 路徑式引用

---

### 方案 4：駝峰式語義化（程式碼風格）

**格式**：`{FeatureName}{UserStoryName}{SceneName}`（駝峰式）

**範例**：
- `PrematchCommentPublishCommentUserLoggedInWithNickname`
- `PrematchCommentLikeCommentUserLoggedIn`
- `PrematchCommentLikeCommentUserNotLoggedIn`
- `LiveChatSendMessageUserLoggedInWithNickname`

**優點**：
- ✅ 符合程式碼命名慣例
- ✅ 適合在程式碼中使用

**缺點**：
- ❌ 沒有分隔符，可讀性較差
- ❌ 長度最長（40-60 字元）

**適用場景**：
- 程式碼變數命名
- 測試函數命名
- 常數定義

---

## 推薦方案：方案 2（混合方案）

### 3.1 命名規則詳解

#### Feature Prefix 對照表

| Feature 全名 | Feature Prefix | 說明 |
|-------------|---------------|------|
| PrematchComment | `PC` | 賽前評論功能 |
| LiveChat | `LC` | 即時聊天功能 |
| RaceDetail | `RD` | 賽事詳情功能 |
| UserProfile | `UP` | 用戶個人資料功能 |

**命名原則**：
- 使用 Feature 名稱的縮寫（2-3 個字母）
- 統一使用大寫字母
- 建立 Feature Prefix 對照表，方便查閱

---

#### UserStory 命名規則

**格式**：使用動詞+名詞的組合，採用 PascalCase

**範例**：
- `PublishComment`：發表評論
- `LikeComment`：點讚評論
- `ReplyComment`：回覆評論
- `SendMessage`：發送訊息
- `JoinChatroom`：加入聊天室
- `BlockUser`：封鎖用戶

**命名原則**：
- 使用動詞開頭（Publish、Like、Reply、Send、Join、Block）
- 使用名詞結尾（Comment、Message、Chatroom、User）
- 採用 PascalCase（每個單字首字母大寫）
- 保持簡潔（2-3 個單字）

---

#### Scene 命名規則

**格式**：使用描述性名詞或形容詞+名詞，採用 PascalCase

**範例**：
- `UserLoggedIn`：用戶已登入
- `UserNotLoggedIn`：用戶未登入
- `UserLoggedInWithNickname`：用戶已登入且有 nickname
- `UserLoggedInWithoutNickname`：用戶已登入但無 nickname
- `NetworkError`：網路錯誤
- `ValidationError`：驗證錯誤
- `EmptyState`：空狀態
- `LoadingState`：載入中狀態

**命名原則**：
- 使用描述性名詞或形容詞+名詞
- 採用 PascalCase（每個單字首字母大寫）
- 保持簡潔（2-4 個單字）
- 避免使用動詞（Scene 是狀態描述，不是動作描述）

---

### 3.2 完整範例

#### 範例 1：PrematchComment Feature

```yaml
user_stories:
  - story_id: "PC-PublishComment"
    role: "用戶"
    action: "在賽事開始前發表評論"
    goal: "與其他用戶互動"
    priority: "P0"
    scenarios:
      - scenario_id: "PC-PublishComment-UserLoggedInWithNickname"
        name: "已登入且有 nickname 的用戶發表評論"
        description: "當用戶已登入且已有 nickname 時，可以直接發表評論"
      - scenario_id: "PC-PublishComment-UserLoggedInWithoutNickname"
        name: "已登入但無 nickname 的用戶發表評論"
        description: "當用戶已登入但沒有 nickname 時，需要先建立 nickname 再發表評論"
      - scenario_id: "PC-PublishComment-UserNotLoggedIn"
        name: "未登入用戶發表評論"
        description: "當用戶未登入時，需要先登入再發表評論"
  
  - story_id: "PC-LikeComment"
    role: "用戶"
    action: "點讚其他用戶的評論"
    goal: "表達對評論的認同"
    priority: "P1"
    scenarios:
      - scenario_id: "PC-LikeComment-UserLoggedIn"
        name: "使用者已登入"
        description: "當用戶已登入時點擊 Like 按鈕的行為"
      - scenario_id: "PC-LikeComment-UserNotLoggedIn"
        name: "使用者未登入"
        description: "當用戶未登入時點擊 Like 按鈕的行為"
  
  - story_id: "PC-ReplyComment"
    role: "用戶"
    action: "回覆其他用戶的評論"
    goal: "與其他用戶討論"
    priority: "P1"
    scenarios:
      - scenario_id: "PC-ReplyComment-UserLoggedInWithNickname"
        name: "已登入且有 nickname 的用戶回覆評論"
        description: "當用戶已登入且已有 nickname 時，可以直接回覆評論"
      - scenario_id: "PC-ReplyComment-UserNotLoggedIn"
        name: "未登入用戶回覆評論"
        description: "當用戶未登入時，需要先登入再回覆評論"
```

---

#### 範例 2：LiveChat Feature

```yaml
user_stories:
  - story_id: "LC-SendMessage"
    role: "用戶"
    action: "發送聊天訊息"
    goal: "與其他用戶即時互動"
    priority: "P0"
    scenarios:
      - scenario_id: "LC-SendMessage-UserLoggedInWithNickname"
        name: "已登入且有 nickname 的用戶發送訊息"
        description: "當用戶已登入且已有 nickname 時，可以直接發送訊息"
      - scenario_id: "LC-SendMessage-UserNotLoggedIn"
        name: "未登入用戶發送訊息"
        description: "當用戶未登入時，需要先登入再發送訊息"
  
  - story_id: "LC-JoinChatroom"
    role: "用戶"
    action: "加入聊天室"
    goal: "參與即時聊天"
    priority: "P0"
    scenarios:
      - scenario_id: "LC-JoinChatroom-UserLoggedIn"
        name: "已登入用戶加入聊天室"
        description: "當用戶已登入時，可以加入聊天室"
      - scenario_id: "LC-JoinChatroom-UserNotLoggedIn"
        name: "未登入用戶加入聊天室"
        description: "當用戶未登入時，需要先登入再加入聊天室"
  
  - story_id: "LC-BlockUser"
    role: "用戶"
    action: "封鎖其他用戶"
    goal: "避免看到不想要的訊息"
    priority: "P1"
    scenarios:
      - scenario_id: "LC-BlockUser-UserLoggedIn"
        name: "已登入用戶封鎖其他用戶"
        description: "當用戶已登入時，可以封鎖其他用戶"
      - scenario_id: "LC-BlockUser-UserNotLoggedIn"
        name: "未登入用戶封鎖其他用戶"
        description: "當用戶未登入時，需要先登入再封鎖其他用戶"
```

---

### 3.3 命名規範檢查清單

#### Feature Prefix 檢查

- [ ] Feature Prefix 使用 2-3 個大寫字母
- [ ] Feature Prefix 在對照表中已定義
- [ ] Feature Prefix 與 Feature 全名對應關係明確

#### UserStory 命名檢查

- [ ] UserStory 使用動詞+名詞組合
- [ ] UserStory 採用 PascalCase
- [ ] UserStory 保持簡潔（2-3 個單字）
- [ ] UserStory 語義清晰，一看就懂

#### Scene 命名檢查

- [ ] Scene 使用描述性名詞或形容詞+名詞
- [ ] Scene 採用 PascalCase
- [ ] Scene 保持簡潔（2-4 個單字）
- [ ] Scene 避免使用動詞（Scene 是狀態描述）
- [ ] Scene 語義清晰，一看就懂

#### 完整場景 ID 檢查

- [ ] 場景 ID 格式正確：`{FeaturePrefix}-{UserStory}-{Scene}`
- [ ] 場景 ID 長度適中（25-40 字元）
- [ ] 場景 ID 語義清晰，一看就懂
- [ ] 場景 ID 唯一性（不重複）

---

## 命名範例對照表

### PrematchComment Feature

| 場景 ID | 場景名稱 | 描述 |
|---------|---------|------|
| `PC-PublishComment-UserLoggedInWithNickname` | 已登入且有 nickname 的用戶發表評論 | 當用戶已登入且已有 nickname 時，可以直接發表評論 |
| `PC-PublishComment-UserLoggedInWithoutNickname` | 已登入但無 nickname 的用戶發表評論 | 當用戶已登入但沒有 nickname 時，需要先建立 nickname 再發表評論 |
| `PC-PublishComment-UserNotLoggedIn` | 未登入用戶發表評論 | 當用戶未登入時，需要先登入再發表評論 |
| `PC-LikeComment-UserLoggedIn` | 使用者已登入 | 當用戶已登入時點擊 Like 按鈕的行為 |
| `PC-LikeComment-UserNotLoggedIn` | 使用者未登入 | 當用戶未登入時點擊 Like 按鈕的行為 |
| `PC-ReplyComment-UserLoggedInWithNickname` | 已登入且有 nickname 的用戶回覆評論 | 當用戶已登入且已有 nickname 時，可以直接回覆評論 |
| `PC-ReplyComment-UserNotLoggedIn` | 未登入用戶回覆評論 | 當用戶未登入時，需要先登入再回覆評論 |

---

### LiveChat Feature

| 場景 ID | 場景名稱 | 描述 |
|---------|---------|------|
| `LC-SendMessage-UserLoggedInWithNickname` | 已登入且有 nickname 的用戶發送訊息 | 當用戶已登入且已有 nickname 時，可以直接發送訊息 |
| `LC-SendMessage-UserNotLoggedIn` | 未登入用戶發送訊息 | 當用戶未登入時，需要先登入再發送訊息 |
| `LC-JoinChatroom-UserLoggedIn` | 已登入用戶加入聊天室 | 當用戶已登入時，可以加入聊天室 |
| `LC-JoinChatroom-UserNotLoggedIn` | 未登入用戶加入聊天室 | 當用戶未登入時，需要先登入再加入聊天室 |
| `LC-BlockUser-UserLoggedIn` | 已登入用戶封鎖其他用戶 | 當用戶已登入時，可以封鎖其他用戶 |
| `LC-BlockUser-UserNotLoggedIn` | 未登入用戶封鎖其他用戶 | 當用戶未登入時，需要先登入再封鎖其他用戶 |

---

## 使用場景

### 4.1 在 PRD 文件中使用

```yaml
user_stories:
  - story_id: "PC-LikeComment"
    role: "用戶"
    action: "點讚其他用戶的評論"
    goal: "表達對評論的認同"
    priority: "P1"
    scenarios:
      - scenario_id: "PC-LikeComment-UserLoggedIn"
        name: "使用者已登入"
        description: "當用戶已登入時點擊 Like 按鈕的行為"
        acceptance_criteria:
          - "用戶可以成功點讚評論"
          - "Like 數立即更新（Optimistic UI）"
        test_cases:
          - "已登入用戶可以點讚評論"
```

---

### 4.2 在設計稿中標註

```
[設計稿截圖：用戶點擊 Like 按鈕的畫面]

場景 ID: PC-LikeComment-UserLoggedIn
場景名稱: 使用者已登入
描述: 當用戶已登入時點擊 Like 按鈕的行為
```

---

### 4.3 在 TDD 文件中引用

```markdown
## Test Scenarios

### Scenario: PC-LikeComment-UserLoggedIn - 使用者已登入

**對應 PRD 場景**: PC-LikeComment-UserLoggedIn  
**對應 Flow**: PC-SUB-004  
**描述**: 當用戶已登入時點擊 Like 按鈕的行為

**測試步驟**:
1. 用戶點擊 Like 按鈕
2. 系統檢查用戶登入狀態
3. 系統立即更新 UI（Optimistic UI）
4. 系統向後端發送請求

**預期結果**:
- 用戶可以看到已點讚狀態
- Like 數增加 1
- 伺服器成功更新 Like 狀態
```

---

### 4.4 在 flow_spec.yaml 中使用

```yaml
features:
  PrematchComment:
    flows:
      - flow_id: PC-SUB-004
        flow_type: Sub
        flow_name: 用戶點擊 Like（含登入檢查）
        scenario_ids:
          - PC-LikeComment-UserLoggedIn
          - PC-LikeComment-UserNotLoggedIn
        scenarios:
          - scenario_id: PC-LikeComment-UserLoggedIn
            name: 使用者已登入
            description: 當用戶已登入時點擊 Like 按鈕的行為
            condition: 使用者已登入
            actions:
              - PrematchComment Package 調用 Main App 的 APICoordinator.shared.accountManager 檢查登入狀態
              - Main App 回應已登入
              - PrematchComment Package 通過登入檢查
              - PrematchComment Package 立即更新畫面上的 Like 數（Optimistic UI）
              - PrematchComment Package 顯示已點讚狀態 + Like +1
              - PrematchComment Package 向伺服器發送 Like 請求
```

---

### 4.5 在程式碼註解中使用

```swift
// 場景 ID: PC-LikeComment-UserLoggedIn
// 場景名稱: 使用者已登入
// 描述: 當用戶已登入時點擊 Like 按鈕的行為
func toggleLike(commentId: String) {
    // 實作邏輯
}
```

---

### 4.6 在測試案例中使用

```swift
// 場景 ID: PC-LikeComment-UserLoggedIn
// 場景名稱: 使用者已登入
func testLikeComment_UserLoggedIn() {
    // 測試邏輯
}
```

---

## 命名規則總結

### 核心原則

1. **語義化優先**：場景 ID 應該能夠直接表達場景的業務含義
2. **簡潔性**：在保持語義清晰的前提下，盡量簡潔
3. **一致性**：整個團隊使用統一的命名規則
4. **可讀性**：場景 ID 應該易於閱讀和理解

### 命名格式

```
{FeaturePrefix}-{UserStory}-{Scene}
```

- **FeaturePrefix**：Feature 縮寫（2-3 個大寫字母）
- **UserStory**：使用者故事名稱（動詞+名詞，PascalCase）
- **Scene**：場景名稱（描述性名詞或形容詞+名詞，PascalCase）

### 命名範例

- ✅ `PC-LikeComment-UserLoggedIn`（推薦）
- ✅ `PC-PublishComment-UserLoggedInWithNickname`（推薦）
- ❌ `PC-US-002-SC-001`（不推薦，語義不清）
- ❌ `PC-Like-UserLogin`（不推薦，UserStory 和 Scene 不夠語義化）

---

## 遷移指南

### 從舊格式遷移到新格式

**舊格式**：`PC-US-002-SC-001`

**新格式**：`PC-LikeComment-UserLoggedIn`

**遷移步驟**：

1. **建立對照表**
   - 列出所有舊場景 ID
   - 為每個舊場景 ID 分配新場景 ID
   - 建立對照表

2. **更新文件**
   - 更新 PRD 文件中的場景 ID
   - 更新設計稿中的場景 ID 標註
   - 更新 TDD 文件中的場景 ID 引用
   - 更新 flow_spec.yaml 中的場景 ID

3. **更新程式碼**
   - 更新程式碼註解中的場景 ID
   - 更新測試案例中的場景 ID

4. **驗證**
   - 檢查所有場景 ID 是否已更新
   - 檢查場景 ID 對應關係是否正確
   - 檢查場景覆蓋率是否完整

---

## 參考資料

- [PRD User Story 場景 Unique ID 提案分析](./SCENARIO_ID_PROPOSAL.md)
- [場景 ID 痛點分析：溝通與文件實作](./SCENARIO_ID_PAIN_POINTS_ANALYSIS.md)
- [TDD Input 資料處理規範](../TDD_rules/tdd_input_processing_rules.md)

