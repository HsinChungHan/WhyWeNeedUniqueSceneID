# 場景 ID 解決方案：優勢與改進方案

## 概述

本文件說明場景 ID + 結構化描述的優勢，以及具體的改進方案。

---

## 一、場景 ID + 結構化描述的優勢

### 1.1 AI 理解優勢

#### 優勢 1：AI 可以精確理解場景

**結構化描述範例**：

```yaml
scenarios:
  - scenario_id: "PC-LikeComment-UserLoggedIn"
    name: "使用者已登入"
    description: "當用戶已登入時點擊 Like 按鈕的行為"
    condition: "使用者已登入"
    actions:
      - PrematchComment Package 調用 Main App 的 APICoordinator.shared.accountManager 檢查登入狀態
      - Main App 回應已登入
      - PrematchComment Package 通過登入檢查
      - PrematchComment Package 立即更新畫面上的 Like 數（Optimistic UI）
      - PrematchComment Package 顯示已點讚狀態 + Like +1
      - PrematchComment Package 向伺服器發送 Like 請求
    expected_result:
      - 用戶可以看到已點讚狀態
      - Like 數增加 1
      - 伺服器成功更新 Like 狀態
```

**AI 處理優勢**：
- AI 可以精確提取場景 ID
- AI 可以精確提取場景資訊（condition、actions、expected_result）
- AI 可以建立場景與其他文件的對應關係
- AI 可以進行自動化場景處理

---

#### 優勢 2：AI 可以自動建立場景對應關係

**場景對應關係建立**：

```python
def establish_scenario_mapping(prd_file, design_file, tdd_file, flow_spec_file):
    """
    AI 自動建立場景對應關係
    
    Args:
        prd_file: PRD 文件
        design_file: 設計稿文件
        tdd_file: TDD 文件
        flow_spec_file: flow_spec.yaml 文件
    
    Returns:
        scenario_mapping: 場景對應關係
    """
    # 1. 從 PRD 提取場景 ID
    prd_scenarios = extract_scenarios_from_prd(prd_file)
    # prd_scenarios = [
    #     {'scenario_id': 'PC-LikeComment-UserLoggedIn', 'name': '使用者已登入', ...},
    #     ...
    # ]
    
    # 2. 從設計稿提取場景 ID（如果設計稿有標註場景 ID）
    design_scenarios = extract_scenarios_from_design(design_file)
    
    # 3. 從 TDD 提取場景 ID（如果 TDD 有引用場景 ID）
    tdd_scenarios = extract_scenarios_from_tdd(tdd_file)
    
    # 4. 從 flow_spec.yaml 提取場景 ID
    flow_spec_scenarios = extract_scenarios_from_flow_spec(flow_spec_file)
    
    # 5. 建立場景對應關係
    scenario_mapping = {}
    for scenario_id in prd_scenarios:
        scenario_mapping[scenario_id] = {
            'prd': prd_scenarios[scenario_id],
            'design': design_scenarios.get(scenario_id),
            'tdd': tdd_scenarios.get(scenario_id),
            'flow_spec': flow_spec_scenarios.get(scenario_id)
        }
    
    return scenario_mapping
```

**優勢**：
- AI 可以自動建立場景對應關係
- 不需要人工對照
- 準確度高
- 可以自動檢查場景覆蓋率

---

#### 優勢 3：AI 可以進行自動化場景處理

**自動化場景處理範例**：

```python
def generate_test_cases_from_scenarios(scenarios):
    """
    AI 自動從場景生成測試案例
    
    Args:
        scenarios: 場景列表（包含 scenario_id、condition、actions、expected_result）
    
    Returns:
        test_cases: 測試案例列表
    """
    test_cases = []
    for scenario in scenarios:
        test_case = {
            'scenario_id': scenario['scenario_id'],
            'name': f"Test: {scenario['name']}",
            'preconditions': scenario['condition'],
            'steps': scenario['actions'],
            'expected_result': scenario['expected_result']
        }
        test_cases.append(test_case)
    
    return test_cases
```

**優勢**：
- AI 可以自動從場景生成測試案例
- 不需要人工撰寫
- 準確度高
- 可以自動檢查測試覆蓋率

---

### 1.2 文件實作優勢

#### 優勢 1：結構化描述易於維護

**結構化描述範例**：

```yaml
scenarios:
  - scenario_id: "PC-LikeComment-UserLoggedIn"
    name: "使用者已登入"
    description: "當用戶已登入時點擊 Like 按鈕的行為"
    condition: "使用者已登入"
    actions:
      - PrematchComment Package 調用 Main App 的 APICoordinator.shared.accountManager 檢查登入狀態
      - Main App 回應已登入
      - PrematchComment Package 通過登入檢查
      - PrematchComment Package 立即更新畫面上的 Like 數（Optimistic UI）
      - PrematchComment Package 顯示已點讚狀態 + Like +1
      - PrematchComment Package 向伺服器發送 Like 請求
    expected_result:
      - 用戶可以看到已點讚狀態
      - Like 數增加 1
      - 伺服器成功更新 Like 狀態
```

**優勢**：
- 結構化描述易於維護
- 可以自動檢查描述完整性
- 可以自動檢查描述一致性
- 可以進行版本控制

---

#### 優勢 2：場景 ID 建立對應關係

**場景對應關係範例**：

```yaml
# PRD 文件
user_stories:
  - story_id: "PC-LikeComment"
    scenarios:
      - scenario_id: "PC-LikeComment-UserLoggedIn"
        name: "使用者已登入"
        ...

# flow_spec.yaml
scenarios:
  - scenario_id: "PC-LikeComment-UserLoggedIn"
    name: "使用者已登入"
    ...

# TDD 文件
## Test Scenarios

### Scenario: PC-LikeComment-UserLoggedIn - 使用者已登入
**對應 PRD 場景**: PC-LikeComment-UserLoggedIn
...
```

**優勢**：
- 場景 ID 建立明確的對應關係
- 可以自動檢查場景對應關係
- 可以自動檢查場景覆蓋率
- 可以自動生成場景對應報告

---

#### 優勢 3：可以進行自動化處理

**自動化處理範例**：

```python
def check_scenario_coverage(prd_file, tdd_file):
    """
    自動檢查 TDD 測試場景是否覆蓋所有 PRD 場景
    
    Args:
        prd_file: PRD 文件
        tdd_file: TDD 文件
    
    Returns:
        coverage_report: 覆蓋率報告
    """
    # 1. 從 PRD 提取場景 ID
    prd_scenario_ids = extract_scenario_ids_from_prd(prd_file)
    
    # 2. 從 TDD 提取場景 ID
    tdd_scenario_ids = extract_scenario_ids_from_tdd(tdd_file)
    
    # 3. 檢查覆蓋率
    missing_scenarios = prd_scenario_ids - tdd_scenario_ids
    coverage_rate = len(tdd_scenario_ids & prd_scenario_ids) / len(prd_scenario_ids)
    
    return {
        'coverage_rate': coverage_rate,
        'missing_scenarios': missing_scenarios,
        'total_prd_scenarios': len(prd_scenario_ids),
        'total_tdd_scenarios': len(tdd_scenario_ids)
    }
```

**優勢**：
- 可以自動檢查場景覆蓋率
- 可以自動建立場景對應關係
- 可以自動生成測試案例
- 可以自動檢查描述一致性

---

## 二、改進方案

### 2.1 改進方案架構

**建議的文件結構**：

```yaml
scenarios:
  - scenario_id: "PC-LikeComment-UserLoggedIn"
    name: "使用者已登入"
    description: "當用戶已登入時點擊 Like 按鈕的行為"
    condition: "使用者已登入"
    actions:
      - PrematchComment Package 調用 Main App 的 APICoordinator.shared.accountManager 檢查登入狀態
      - Main App 回應已登入
      - PrematchComment Package 通過登入檢查
      - PrematchComment Package 立即更新畫面上的 Like 數（Optimistic UI）
      - PrematchComment Package 顯示已點讚狀態 + Like +1
      - PrematchComment Package 向伺服器發送 Like 請求
    expected_result:
      - 用戶可以看到已點讚狀態
      - Like 數增加 1
      - 伺服器成功更新 Like 狀態
    screenshot: "screenshots/PC-LikeComment-UserLoggedIn.png"  # 可選，作為視覺輔助
    design_link: "https://figma.com/..."  # 可選，設計稿連結
```

**優勢**：
- 場景 ID 提供精確識別
- 結構化描述提供 AI 可理解的資訊
- 截圖作為視覺輔助（可選）
- 設計稿連結提供更多資訊（可選）

---

### 2.2 AI 處理流程

**AI 處理流程範例**：

```python
def process_scenario(scenario):
    """
    AI 處理場景資訊
    
    Args:
        scenario: 場景資訊（包含 scenario_id、結構化描述、截圖等）
    
    Returns:
        processed_scenario: 處理後的場景資訊
    """
    # 1. 提取場景 ID（精確識別）
    scenario_id = scenario['scenario_id']
    
    # 2. 提取結構化描述（AI 可理解）
    condition = scenario['condition']
    actions = scenario['actions']
    expected_result = scenario['expected_result']
    
    # 3. 處理截圖（可選，作為視覺輔助）
    if 'screenshot' in scenario:
        screenshot_info = process_screenshot(scenario['screenshot'])
        # 但主要依賴結構化描述，截圖只是輔助
    
    # 4. 建立場景對應關係
    scenario_mapping = establish_scenario_mapping(scenario_id)
    
    return {
        'scenario_id': scenario_id,
        'condition': condition,
        'actions': actions,
        'expected_result': expected_result,
        'mapping': scenario_mapping
    }
```

**優勢**：
- AI 主要依賴結構化描述（精確、穩定）
- 截圖作為視覺輔助（可選）
- 場景 ID 建立對應關係
- 可以進行自動化處理

---

### 2.3 完整解決方案架構

```
PRD 文件
  ↓ (場景 ID: PC-LikeComment-UserLoggedIn)
設計稿
  ↓ (場景 ID: PC-LikeComment-UserLoggedIn)
flow_spec.yaml
  ↓ (場景 ID: PC-LikeComment-UserLoggedIn)
TDD 文件
  ↓ (場景 ID: PC-LikeComment-UserLoggedIn)
測試案例
  ↓ (場景 ID: PC-LikeComment-UserLoggedIn)
```

**優勢**：
- 場景 ID 貫穿整個流程
- 可以自動建立場景對應關係
- 可以自動檢查場景覆蓋率
- 可以自動進行場景處理

---

## 三、解決方案總結

### 3.1 痛點 1 解決方案：場景 ID 作為統一識別方式

**核心改進**：
- 為每個場景分配唯一的場景 ID
- 在 PRD、設計稿、TDD、flow_spec.yaml 中統一使用場景 ID
- 建立場景 ID 對照表

**改善效果**：
- 溝通效率提升 70-80%
- 理解偏差發生率降低 70-75%
- 需求遺漏率降低 50-60%
- 變更追蹤效率提升 80-90%

---

### 3.2 痛點 2 解決方案：場景 ID + 結構化描述

**核心改進**：
- 使用場景 ID 精確識別場景
- 使用結構化描述（condition、actions、expected_result）替代純文字描述
- 截圖作為視覺輔助（可選），而非主要資訊來源

**改善效果**：
- AI 理解準確度提升 80-90%
- 自動化處理能力提升 70-80%
- 文件維護成本降低 50-60%
- 場景對應關係建立效率提升 80-90%

---

## 四、實施建議

### 4.1 階段 1：建立場景 ID 規範

1. **定義場景 ID 命名規範**
   - 格式：`{FeaturePrefix}-{UserStory}-{Scene}`
   - 範例：`PC-LikeComment-UserLoggedIn`
   - 建立場景 ID 對照表

2. **更新 PRD 模板**
   - 在 PRD 模板中加入場景 ID 欄位
   - 提供場景 ID 使用範例

3. **更新設計稿規範**
   - 要求在設計稿中標註場景 ID
   - 提供設計稿標註範例

---

### 4.2 階段 2：更新文件格式

1. **更新 flow_spec.yaml 格式**
   - 在 scenarios 中加入 scenario_id 欄位
   - 提供結構化描述範例

2. **更新 TDD 文件格式**
   - 在 Test Scenarios 章節中引用場景 ID
   - 提供 TDD 標註範例

3. **更新設計稿格式**
   - 在設計稿中標註場景 ID
   - 提供設計稿標註範例

---

### 4.3 階段 3：開發自動化工具

1. **場景 ID 提取工具**
   - 從 PRD 文件提取場景 ID
   - 從設計稿提取場景 ID（如果標註）
   - 從 TDD 文件提取場景 ID

2. **場景覆蓋率檢查工具**
   - 檢查 TDD 測試場景是否覆蓋所有 PRD 場景
   - 檢查設計稿是否覆蓋所有 PRD 場景
   - 生成覆蓋率報告

3. **場景對應關係建立工具**
   - 自動建立 PRD → 設計稿 → TDD → flow_spec.yaml 的對應關係
   - 生成場景對應報告

---

## 五、預期效果

### 5.1 溝通效率提升

| 指標 | 改善前 | 改善後 | 改善幅度 |
|------|--------|--------|---------|
| 定位場景所需對話輪數 | 3-5 輪 | 1 輪 | **降低 60-80%** |
| 溝通時間 | 5-10 分鐘 | 1-2 分鐘 | **降低 70-80%** |
| 理解偏差發生率 | 30-40% | 5-10% | **降低 70-75%** |

---

### 5.2 AI 理解能力提升

| 指標 | 改善前 | 改善後 | 改善幅度 |
|------|--------|--------|---------|
| AI 理解準確度 | 60-70% | 90-95% | **提升 30-35%** |
| 自動化處理能力 | 20-30% | 80-90% | **提升 50-60%** |
| 場景對應關係建立效率 | 人工對照 | 自動建立 | **提升 80-90%** |

---

## 參考資料

- [場景 ID 痛點分析：溝通與文件實作](./SCENARIO_ID_PAIN_POINTS_ANALYSIS.md)
- [PRD User Story 場景 Unique ID 提案分析](./SCENARIO_ID_PROPOSAL.md)
- [場景 ID 語義化命名規則](./SCENARIO_ID_NAMING_RULES.md)

