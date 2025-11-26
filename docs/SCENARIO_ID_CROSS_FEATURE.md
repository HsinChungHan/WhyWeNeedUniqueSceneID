# 場景 ID 跨 Feature 處理規範

## 概述

本文件說明當不同 Feature 都有相同或類似的場景（如登入登出流程）時，場景 ID 的處理方式與統整策略。

---

## 問題分析

### 問題場景

**情境**：多個 Feature 都有登入檢查流程

- `PrematchComment` Feature：點讚時需要檢查登入狀態
- `LiveChat` Feature：發送訊息時需要檢查登入狀態
- `RaceDetail` Feature：查看詳情時需要檢查登入狀態

**問題**：
- 這些登入場景應該用同一個 ID 嗎？
- 還是每個 Feature 都有自己的登入場景 ID？
- 如何統整不同 Feature 的場景？

---

## 方案比較

### 方案 A：每個 Feature 獨立的場景 ID（推薦）

**格式**：`{FeaturePrefix}-{UserStory}-{Scene}`

**範例**：
- `PC-LikeComment-UserLoggedIn`：PrematchComment 的點讚場景（已登入）
- `LC-SendMessage-UserLoggedIn`：LiveChat 的發送訊息場景（已登入）
- `RD-ViewDetail-UserLoggedIn`：RaceDetail 的查看詳情場景（已登入）

**優點**：
- ✅ 場景 ID 明確對應到具體的業務場景
- ✅ 每個 Feature 的場景獨立，不會混淆
- ✅ 符合「場景 ID 對應到 User Story」的原則
- ✅ 易於追蹤和維護

**缺點**：
- ❌ 如果登入流程完全相同，會有多個相似的場景 ID
- ❌ 無法直接看出這些場景共用同一個登入流程

**適用場景**：
- 不同 Feature 的登入流程雖然相似，但業務上下文不同
- 需要追蹤每個 Feature 的具體場景
- 登入流程可能因 Feature 而略有差異

---

### 方案 B：共用場景 ID（不推薦）

**格式**：`Shared-{SharedModule}-{Scene}`

**範例**：
- `Shared-Auth-UserLoggedIn`：共用的登入場景
- `Shared-Auth-UserNotLoggedIn`：共用的未登入場景

**優點**：
- ✅ 如果登入流程完全相同，可以共用同一個場景 ID
- ✅ 減少場景 ID 數量

**缺點**：
- ❌ 無法區分不同 Feature 的業務上下文
- ❌ 無法追蹤每個 Feature 的具體場景
- ❌ 違反「場景 ID 對應到 User Story」的原則
- ❌ 如果登入流程因 Feature 而異，會造成混淆

**適用場景**：
- 登入流程完全相同，且不依賴 Feature 上下文
- 純技術性的共用流程（不建議用於業務場景）

---

### 方案 C：混合方案（推薦用於 Shared Feature）

**格式**：
- Feature 場景：`{FeaturePrefix}-{UserStory}-{Scene}`
- Shared Feature 場景：`Shared-{SharedFeature}-{Scene}`

**範例**：
- `PC-LikeComment-UserLoggedIn`：PrematchComment 的點讚場景（已登入）
- `LC-SendMessage-UserLoggedIn`：LiveChat 的發送訊息場景（已登入）
- `Shared-Auth-LoginFlow`：共用的登入流程（如果登入本身是一個獨立的 User Story）

**優點**：
- ✅ Feature 場景保持獨立，對應到具體的 User Story
- ✅ Shared Feature 場景可以共用
- ✅ 可以區分業務場景和技術流程

**缺點**：
- ❌ 需要明確區分 Feature 場景和 Shared Feature 場景
- ❌ 命名規則稍微複雜

**適用場景**：
- 有明確的 Shared Feature（如 AuthFeature、NicknameFeature）
- 需要區分業務場景和技術流程

---

## 推薦方案：方案 A（每個 Feature 獨立的場景 ID）

### 理由

1. **場景 ID 對應到 User Story**
   - 場景 ID 應該對應到具體的 User Story，而不是技術流程
   - `PC-LikeComment-UserLoggedIn` 對應到「點讚評論」這個 User Story 的「已登入」場景
   - `LC-SendMessage-UserLoggedIn` 對應到「發送訊息」這個 User Story 的「已登入」場景

2. **業務上下文不同**
   - 雖然都是登入檢查，但業務上下文不同：
     - PrematchComment 的登入檢查是在點讚時觸發
     - LiveChat 的登入檢查是在發送訊息時觸發
   - 這些場景的驗收條件、測試案例可能不同

3. **易於追蹤和維護**
   - 每個 Feature 的場景獨立，不會混淆
   - 可以精確追蹤每個 Feature 的場景覆蓋率
   - 如果某個 Feature 的登入流程需要調整，不會影響其他 Feature

---

## 跨 Feature 場景統整策略

### 策略 1：場景對照表（推薦）

**建立場景對照表**，標註哪些場景共用相同的技術流程：

| 場景 ID | 場景名稱 | 所屬 Feature | 共用技術流程 | 描述 |
|---------|---------|-------------|------------|------|
| `PC-LikeComment-UserLoggedIn` | 使用者已登入 | PrematchComment | `Shared-Auth-LoginFlow` | 當用戶已登入時點擊 Like 按鈕的行為 |
| `LC-SendMessage-UserLoggedIn` | 使用者已登入 | LiveChat | `Shared-Auth-LoginFlow` | 當用戶已登入時發送訊息的行為 |
| `RD-ViewDetail-UserLoggedIn` | 使用者已登入 | RaceDetail | `Shared-Auth-LoginFlow` | 當用戶已登入時查看詳情的行為 |

**使用方式**：
- 場景 ID 保持獨立（對應到 User Story）
- 透過「共用技術流程」欄位標註共用的技術流程
- 可以查詢哪些場景共用相同的技術流程

---

### 策略 2：場景標籤系統

**在場景描述中加入標籤**，標註共用的技術流程：

```yaml
scenarios:
  - scenario_id: "PC-LikeComment-UserLoggedIn"
    name: "使用者已登入"
    description: "當用戶已登入時點擊 Like 按鈕的行為"
    tags:
      - "auth"
      - "login-check"
    shared_flows:
      - "Shared-Auth-LoginFlow"
    ...
```

**使用方式**：
- 場景 ID 保持獨立
- 透過 `tags` 和 `shared_flows` 標註共用的技術流程
- 可以根據標籤查詢相關場景

---

### 策略 3：場景關係圖

**建立場景關係圖**，視覺化展示場景之間的關係：

```
PC-LikeComment-UserLoggedIn
    ↓ (uses)
Shared-Auth-LoginFlow
    ↑ (used by)
LC-SendMessage-UserLoggedIn
    ↑ (used by)
RD-ViewDetail-UserLoggedIn
```

**使用方式**：
- 場景 ID 保持獨立
- 透過關係圖展示場景之間的依賴關係
- 可以視覺化理解場景的共用關係

---

## 實際應用範例

### 範例 1：登入檢查場景

**不同 Feature 的登入檢查場景**：

```yaml
# PrematchComment Feature
user_stories:
  - story_id: "PC-LikeComment"
    scenarios:
      - scenario_id: "PC-LikeComment-UserLoggedIn"
        name: "使用者已登入"
        description: "當用戶已登入時點擊 Like 按鈕的行為"
        shared_flows:
          - "Shared-Auth-LoginCheck"
        ...

# LiveChat Feature
user_stories:
  - story_id: "LC-SendMessage"
    scenarios:
      - scenario_id: "LC-SendMessage-UserLoggedIn"
        name: "使用者已登入"
        description: "當用戶已登入時發送訊息的行為"
        shared_flows:
          - "Shared-Auth-LoginCheck"
        ...
```

**說明**：
- 每個 Feature 有自己的場景 ID
- 透過 `shared_flows` 標註共用的技術流程
- 場景 ID 對應到具體的 User Story

---

### 範例 2：Nickname 檢查場景

**不同 Feature 的 Nickname 檢查場景**：

```yaml
# PrematchComment Feature
user_stories:
  - story_id: "PC-PublishComment"
    scenarios:
      - scenario_id: "PC-PublishComment-UserLoggedInWithNickname"
        name: "已登入且有 nickname"
        description: "已登入且有 nickname 的用戶發表評論"
        shared_flows:
          - "Shared-Auth-LoginCheck"
          - "Shared-Nickname-Check"
        ...

# LiveChat Feature
user_stories:
  - story_id: "LC-SendMessage"
    scenarios:
      - scenario_id: "LC-SendMessage-UserLoggedInWithNickname"
        name: "已登入且有 nickname"
        description: "已登入且有 nickname 的用戶發送訊息"
        shared_flows:
          - "Shared-Auth-LoginCheck"
          - "Shared-Nickname-Check"
        ...
```

**說明**：
- 每個 Feature 有自己的場景 ID
- 透過 `shared_flows` 標註多個共用的技術流程
- 場景 ID 對應到具體的 User Story

---

## 場景統整工具

### 工具 1：場景查詢工具

**功能**：根據共用技術流程查詢相關場景

```python
def find_scenarios_by_shared_flow(shared_flow_id):
    """
    根據共用技術流程查詢相關場景
    
    Args:
        shared_flow_id: 共用技術流程 ID（如 "Shared-Auth-LoginCheck"）
    
    Returns:
        scenarios: 相關場景列表
    """
    scenarios = []
    for feature in all_features:
        for story in feature.user_stories:
            for scenario in story.scenarios:
                if shared_flow_id in scenario.shared_flows:
                    scenarios.append({
                        'scenario_id': scenario.scenario_id,
                        'feature': feature.name,
                        'user_story': story.story_id,
                        'name': scenario.name
                    })
    return scenarios
```

---

### 工具 2：場景覆蓋率檢查工具

**功能**：檢查共用技術流程的場景覆蓋率

```python
def check_shared_flow_coverage(shared_flow_id):
    """
    檢查共用技術流程的場景覆蓋率
    
    Args:
        shared_flow_id: 共用技術流程 ID
    
    Returns:
        coverage_report: 覆蓋率報告
    """
    # 1. 找出所有使用該共用技術流程的場景
    scenarios = find_scenarios_by_shared_flow(shared_flow_id)
    
    # 2. 檢查每個 Feature 是否都有對應的場景
    features_with_scenario = {s['feature'] for s in scenarios}
    all_features = get_all_features()
    missing_features = set(all_features) - features_with_scenario
    
    return {
        'shared_flow_id': shared_flow_id,
        'total_scenarios': len(scenarios),
        'features_with_scenario': list(features_with_scenario),
        'missing_features': list(missing_features),
        'coverage_rate': len(features_with_scenario) / len(all_features)
    }
```

---

### 工具 3：場景對照表生成工具

**功能**：自動生成場景對照表

```python
def generate_scenario_mapping_table():
    """
    自動生成場景對照表
    
    Returns:
        mapping_table: 場景對照表
    """
    table = []
    for feature in all_features:
        for story in feature.user_stories:
            for scenario in story.scenarios:
                row = {
                    'scenario_id': scenario.scenario_id,
                    'scenario_name': scenario.name,
                    'feature': feature.name,
                    'user_story': story.story_id,
                    'shared_flows': scenario.shared_flows,
                    'description': scenario.description
                }
                table.append(row)
    return table
```

---

## 最佳實踐建議

### 1. 場景 ID 命名原則

- **場景 ID 對應到 User Story**：每個場景 ID 應該對應到具體的 User Story，而不是技術流程
- **保持獨立性**：不同 Feature 的場景應該有獨立的場景 ID，即使它們共用相同的技術流程
- **標註共用關係**：透過 `shared_flows` 或 `tags` 標註共用的技術流程

---

### 2. 場景統整原則

- **場景對照表**：建立場景對照表，標註哪些場景共用相同的技術流程
- **場景標籤系統**：使用標籤系統標註共用的技術流程
- **場景關係圖**：建立場景關係圖，視覺化展示場景之間的關係

---

### 3. 工具支援

- **場景查詢工具**：根據共用技術流程查詢相關場景
- **場景覆蓋率檢查工具**：檢查共用技術流程的場景覆蓋率
- **場景對照表生成工具**：自動生成場景對照表

---

## 總結

### 核心原則

1. **場景 ID 對應到 User Story**：每個場景 ID 應該對應到具體的 User Story，而不是技術流程
2. **保持獨立性**：不同 Feature 的場景應該有獨立的場景 ID，即使它們共用相同的技術流程
3. **標註共用關係**：透過 `shared_flows` 或 `tags` 標註共用的技術流程

### 推薦方案

- **場景 ID 命名**：使用 `{FeaturePrefix}-{UserStory}-{Scene}` 格式
- **場景統整**：使用場景對照表、標籤系統、關係圖
- **工具支援**：開發場景查詢、覆蓋率檢查、對照表生成工具

---

## 參考資料

- [場景 ID 語義化命名規則](./SCENARIO_ID_NAMING_RULES.md)
- [場景 ID 痛點分析：溝通與文件實作](./SCENARIO_ID_PAIN_POINTS_ANALYSIS.md)
- [TDD Module Consolidation Rules](../TDD_rules/tdd_module_consolidation_rules.md)

