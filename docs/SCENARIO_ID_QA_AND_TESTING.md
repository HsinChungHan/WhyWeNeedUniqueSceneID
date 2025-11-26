# 場景 ID 對 QA 與測試的價值

## 概述

本文件說明場景 ID 對 QA（Quality Assurance）和 UI Automation Test 的價值，以及場景覆蓋率檢查工具的使用時機和角色。

---

## 一、場景覆蓋率檢查工具的使用時機與角色

### 1.1 使用時機與角色對照表

| 階段 | 角色 | 使用時機 | 檢查內容 | 目的 |
|------|------|---------|---------|------|
| **階段 3：TDD 生成階段** | Client Side RD | TDD 生成後 | PRD 場景 vs TDD 測試場景 | 確保 TDD 測試場景覆蓋所有 PRD 場景 |
| **階段 4：開發與測試階段** | Client Side RD | 開發完成後 | PRD 場景 vs 程式碼場景 ID | 確保程式碼實作覆蓋所有 PRD 場景 |
| **階段 5：測試階段** | QA / Test Engineer | 測試計劃制定時 | PRD 場景 vs 測試計劃 | 確保測試計劃覆蓋所有 PRD 場景 |
| **階段 5：測試階段** | QA / Test Engineer | 測試執行前 | PRD 場景 vs 測試案例 | 確保測試案例覆蓋所有 PRD 場景 |
| **階段 5：測試階段** | QA / Test Engineer | 測試執行後 | PRD 場景 vs 測試結果 | 確保所有場景都已測試 |
| **階段 6：驗收階段** | PM / QA | 功能驗收時 | PRD 場景 vs 驗收結果 | 確保所有場景都已驗收 |
| **持續階段** | QA Lead / Test Manager | 定期檢查 | 整體場景覆蓋率 | 追蹤測試覆蓋率趨勢 |

---

### 1.2 各階段詳細說明

#### 階段 3：TDD 生成階段

**使用角色**：Client Side RD

**使用時機**：
- TDD 文件生成完成後
- 在提交 TDD 文件進行審查前

**檢查內容**：
- PRD 中的場景 ID 列表
- TDD Test Scenarios 章節中的場景 ID 引用

**檢查工具**：

```python
def check_tdd_scenario_coverage(prd_file: str, tdd_file: str) -> dict:
    """
    檢查 TDD 測試場景是否覆蓋所有 PRD 場景
    
    Args:
        prd_file: PRD 文件路徑
        tdd_file: TDD 文件路徑
    
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
        'missing_scenarios': list(missing_scenarios),
        'total_prd_scenarios': len(prd_scenario_ids),
        'total_tdd_scenarios': len(tdd_scenario_ids)
    }
```

**產出**：
- 場景覆蓋率報告
- 遺漏場景清單

**目的**：
- 確保 TDD 測試場景覆蓋所有 PRD 場景
- 避免遺漏重要的測試場景

---

#### 階段 4：開發與測試階段

**使用角色**：Client Side RD

**使用時機**：
- 開發完成後
- 在提交 Code Review 前

**檢查內容**：
- PRD 中的場景 ID 列表
- Codebase 中的場景 ID 標註

**檢查工具**：

```python
def check_codebase_scenario_coverage(prd_file: str, codebase_path: str) -> dict:
    """
    檢查 codebase 是否覆蓋所有 PRD 場景
    
    Args:
        prd_file: PRD 文件路徑
        codebase_path: codebase 根目錄路徑
    
    Returns:
        coverage_report: 覆蓋率報告
    """
    # 1. 從 PRD 提取場景 ID
    prd_scenario_ids = extract_scenario_ids_from_prd(prd_file)
    
    # 2. 從 codebase 提取場景 ID
    codebase_scenario_ids = extract_scenario_ids_from_codebase(codebase_path)
    
    # 3. 檢查覆蓋率
    missing_scenarios = prd_scenario_ids - set(codebase_scenario_ids.keys())
    coverage_rate = len(set(codebase_scenario_ids.keys()) & prd_scenario_ids) / len(prd_scenario_ids)
    
    return {
        'coverage_rate': coverage_rate,
        'missing_scenarios': list(missing_scenarios),
        'total_prd_scenarios': len(prd_scenario_ids),
        'total_codebase_scenarios': len(codebase_scenario_ids),
        'scenario_mapping': codebase_scenario_ids
    }
```

**產出**：
- 場景覆蓋率報告
- 場景 ID 與程式碼的對應關係

**目的**：
- 確保程式碼實作覆蓋所有 PRD 場景
- 追蹤場景 ID 與程式碼的對應關係

---

#### 階段 5：測試階段

**使用角色**：QA / Test Engineer

**使用時機**：
- 測試計劃制定時
- 測試案例撰寫時
- 測試執行前
- 測試執行後

**檢查內容**：
- PRD 中的場景 ID 列表
- 測試計劃中的場景 ID
- 測試案例中的場景 ID
- 測試結果中的場景 ID

**檢查工具**：

```python
def check_test_scenario_coverage(prd_file: str, test_plan_file: str, test_results_file: str) -> dict:
    """
    檢查測試是否覆蓋所有 PRD 場景
    
    Args:
        prd_file: PRD 文件路徑
        test_plan_file: 測試計劃文件路徑
        test_results_file: 測試結果文件路徑
    
    Returns:
        coverage_report: 覆蓋率報告
    """
    # 1. 從 PRD 提取場景 ID
    prd_scenario_ids = extract_scenario_ids_from_prd(prd_file)
    
    # 2. 從測試計劃提取場景 ID
    test_plan_scenario_ids = extract_scenario_ids_from_test_plan(test_plan_file)
    
    # 3. 從測試結果提取場景 ID
    test_results_scenario_ids = extract_scenario_ids_from_test_results(test_results_file)
    
    # 4. 檢查覆蓋率
    missing_in_plan = prd_scenario_ids - test_plan_scenario_ids
    missing_in_results = prd_scenario_ids - test_results_scenario_ids
    coverage_rate = len(test_results_scenario_ids & prd_scenario_ids) / len(prd_scenario_ids)
    
    return {
        'coverage_rate': coverage_rate,
        'missing_in_plan': list(missing_in_plan),
        'missing_in_results': list(missing_in_results),
        'total_prd_scenarios': len(prd_scenario_ids),
        'total_test_plan_scenarios': len(test_plan_scenario_ids),
        'total_test_results_scenarios': len(test_results_scenario_ids)
    }
```

**產出**：
- 測試覆蓋率報告
- 遺漏場景清單
- 測試結果對應關係

**目的**：
- 確保測試計劃覆蓋所有 PRD 場景
- 確保測試案例覆蓋所有 PRD 場景
- 確保所有場景都已測試

---

#### 階段 6：驗收階段

**使用角色**：PM / QA

**使用時機**：
- 功能驗收時
- 發布前檢查

**檢查內容**：
- PRD 中的場景 ID 列表
- 驗收結果中的場景 ID

**檢查工具**：

```python
def check_acceptance_scenario_coverage(prd_file: str, acceptance_results_file: str) -> dict:
    """
    檢查驗收是否覆蓋所有 PRD 場景
    
    Args:
        prd_file: PRD 文件路徑
        acceptance_results_file: 驗收結果文件路徑
    
    Returns:
        coverage_report: 覆蓋率報告
    """
    # 1. 從 PRD 提取場景 ID
    prd_scenario_ids = extract_scenario_ids_from_prd(prd_file)
    
    # 2. 從驗收結果提取場景 ID
    acceptance_scenario_ids = extract_scenario_ids_from_acceptance_results(acceptance_results_file)
    
    # 3. 檢查覆蓋率
    missing_scenarios = prd_scenario_ids - acceptance_scenario_ids
    coverage_rate = len(acceptance_scenario_ids & prd_scenario_ids) / len(prd_scenario_ids)
    
    return {
        'coverage_rate': coverage_rate,
        'missing_scenarios': list(missing_scenarios),
        'total_prd_scenarios': len(prd_scenario_ids),
        'total_acceptance_scenarios': len(acceptance_scenario_ids)
    }
```

**產出**：
- 驗收覆蓋率報告
- 遺漏場景清單

**目的**：
- 確保所有場景都已驗收
- 避免遺漏重要的驗收場景

---

## 二、場景 ID 對 QA 的價值

### 2.1 測試計劃制定

#### 價值 1：系統化測試計劃

**無場景 ID 的情況**：

```
測試計劃：
- 測試點讚功能
- 測試發表評論功能
- 測試回覆評論功能
```

**問題**：
- 測試計劃不夠具體
- 無法對應到 PRD 場景
- 容易遺漏邊界場景

**有場景 ID 的情況**：

```
測試計劃：
- PC-LikeComment-UserLoggedIn：測試已登入用戶點讚
- PC-LikeComment-UserNotLoggedIn：測試未登入用戶點讚
- PC-PublishComment-UserLoggedInWithNickname：測試已登入且有 nickname 的用戶發表評論
- PC-PublishComment-UserLoggedInWithoutNickname：測試已登入但無 nickname 的用戶發表評論
- PC-PublishComment-UserNotLoggedIn：測試未登入用戶發表評論
```

**優勢**：
- ✅ 測試計劃具體明確
- ✅ 可以直接對應到 PRD 場景
- ✅ 可以系統化檢查場景覆蓋率
- ✅ 避免遺漏邊界場景

---

#### 價值 2：測試案例管理

**無場景 ID 的情況**：

```
測試案例：
- test_like_comment_1：測試點讚功能（已登入）
- test_like_comment_2：測試點讚功能（未登入）
- test_publish_comment_1：測試發表評論功能（已登入）
```

**問題**：
- 測試案例名稱不夠語義化
- 無法對應到 PRD 場景
- 無法追蹤測試案例與需求的對應關係

**有場景 ID 的情況**：

```
測試案例：
- test_PC_LikeComment_UserLoggedIn：測試場景 PC-LikeComment-UserLoggedIn
- test_PC_LikeComment_UserNotLoggedIn：測試場景 PC-LikeComment-UserNotLoggedIn
- test_PC_PublishComment_UserLoggedInWithNickname：測試場景 PC-PublishComment-UserLoggedInWithNickname
```

**優勢**：
- ✅ 測試案例名稱語義化
- ✅ 可以直接對應到 PRD 場景
- ✅ 可以追蹤測試案例與需求的對應關係
- ✅ 可以自動檢查測試覆蓋率

---

### 2.2 測試執行與追蹤

#### 價值 1：測試結果追蹤

**無場景 ID 的情況**：

```
測試結果：
- test_like_comment_1：PASS
- test_like_comment_2：FAIL
- test_publish_comment_1：PASS
```

**問題**：
- 無法知道哪些 PRD 場景已測試
- 無法知道哪些 PRD 場景未測試
- 無法追蹤測試結果與需求的對應關係

**有場景 ID 的情況**：

```
測試結果：
- PC-LikeComment-UserLoggedIn：PASS
- PC-LikeComment-UserNotLoggedIn：FAIL
- PC-PublishComment-UserLoggedInWithNickname：PASS
```

**優勢**：
- ✅ 可以直接對應到 PRD 場景
- ✅ 可以追蹤哪些場景已測試
- ✅ 可以追蹤哪些場景未測試
- ✅ 可以生成場景覆蓋率報告

---

#### 價值 2：Bug 追蹤

**無場景 ID 的情況**：

```
Bug 報告：
- Bug #123：點讚功能有問題
- Bug #124：發表評論功能有問題
```

**問題**：
- 無法知道 Bug 對應到哪個 PRD 場景
- 無法統計哪些場景容易出現問題
- 無法追蹤 Bug 修復對場景的影響

**有場景 ID 的情況**：

```
Bug 報告：
- Bug #123：場景 PC-LikeComment-UserLoggedIn 出現問題
- Bug #124：場景 PC-PublishComment-UserLoggedInWithNickname 出現問題
```

**優勢**：
- ✅ 可以直接對應到 PRD 場景
- ✅ 可以統計哪些場景容易出現問題
- ✅ 可以追蹤 Bug 修復對場景的影響
- ✅ 可以進行場景級別的 Bug 分析

---

### 2.3 測試報告生成

#### 價值：自動化測試報告

**無場景 ID 的情況**：

```
測試報告：
- 總測試案例數：50
- 通過：45
- 失敗：5
- 覆蓋率：無法計算
```

**問題**：
- 無法知道測試覆蓋率
- 無法知道哪些場景已測試
- 無法知道哪些場景未測試

**有場景 ID 的情況**：

```
測試報告：
- 總 PRD 場景數：20
- 已測試場景數：18
- 未測試場景數：2
- 場景覆蓋率：90%
- 通過場景：16
- 失敗場景：2
- 未測試場景：PC-PublishComment-UserNotLoggedIn, PC-ReplyComment-UserNotLoggedIn
```

**優勢**：
- ✅ 可以計算場景覆蓋率
- ✅ 可以列出已測試和未測試的場景
- ✅ 可以生成詳細的測試報告
- ✅ 可以追蹤測試覆蓋率趨勢

---

## 三、場景 ID 對 UI Automation Test 的價值

### 3.1 測試案例撰寫

#### 價值 1：測試案例結構化

**無場景 ID 的情況**：

```python
def test_like_comment():
    """測試點讚功能"""
    # 測試邏輯
    pass
```

**問題**：
- 測試案例名稱不夠語義化
- 無法對應到 PRD 場景
- 無法追蹤測試案例與需求的對應關係

**有場景 ID 的情況**：

```python
# Test: PC-LikeComment-UserLoggedIn
# 場景 ID: PC-LikeComment-UserLoggedIn
# 場景名稱: 使用者已登入
# 描述: 當用戶已登入時點擊 Like 按鈕的行為
def test_PC_LikeComment_UserLoggedIn():
    """
    測試場景: PC-LikeComment-UserLoggedIn
    場景名稱: 使用者已登入
    描述: 當用戶已登入時點擊 Like 按鈕的行為
    """
    # Given: 用戶已登入
    # When: 用戶點擊 Like 按鈕
    # Then: 系統立即更新 UI（Optimistic UI），Like 數 +1
    pass
```

**優勢**：
- ✅ 測試案例名稱語義化
- ✅ 可以直接對應到 PRD 場景
- ✅ 可以追蹤測試案例與需求的對應關係
- ✅ 可以自動檢查測試覆蓋率

---

#### 價值 2：測試案例自動生成

**無場景 ID 的情況**：

```
需要手動撰寫所有測試案例
- 無法自動生成
- 容易遺漏場景
- 測試案例不一致
```

**有場景 ID 的情況**：

```python
def generate_ui_test_cases_from_scenarios(scenarios: list) -> list:
    """
    從場景自動生成 UI 測試案例
    
    Args:
        scenarios: 場景列表（包含 scenario_id、condition、actions、expected_result）
    
    Returns:
        test_cases: 測試案例列表
    """
    test_cases = []
    for scenario in scenarios:
        test_case = {
            'name': f"test_{scenario['scenario_id'].replace('-', '_')}",
            'scenario_id': scenario['scenario_id'],
            'description': scenario['description'],
            'preconditions': scenario['condition'],
            'steps': convert_actions_to_ui_steps(scenario['actions']),
            'expected_result': scenario['expected_result']
        }
        test_cases.append(test_case)
    
    return test_cases
```

**優勢**：
- ✅ 可以自動生成測試案例
- ✅ 測試案例結構一致
- ✅ 減少手動撰寫時間
- ✅ 避免遺漏場景

---

### 3.2 測試執行與報告

#### 價值 1：測試結果對應

**無場景 ID 的情況**：

```
測試結果：
- test_like_comment_1：PASS
- test_like_comment_2：FAIL
```

**問題**：
- 無法知道測試結果對應到哪個 PRD 場景
- 無法追蹤哪些場景已測試
- 無法生成場景覆蓋率報告

**有場景 ID 的情況**：

```python
# 測試結果格式
{
    'scenario_id': 'PC-LikeComment-UserLoggedIn',
    'test_name': 'test_PC_LikeComment_UserLoggedIn',
    'status': 'PASS',
    'duration': 1.23,
    'steps': [
        {'action': '點擊 Like 按鈕', 'status': 'PASS'},
        {'action': '檢查 UI 更新', 'status': 'PASS'}
    ]
}
```

**優勢**：
- ✅ 可以直接對應到 PRD 場景
- ✅ 可以追蹤哪些場景已測試
- ✅ 可以生成場景覆蓋率報告
- ✅ 可以進行場景級別的測試分析

---

#### 價值 2：測試報告自動生成

**無場景 ID 的情況**：

```
測試報告：
- 總測試案例數：50
- 通過：45
- 失敗：5
```

**問題**：
- 無法知道測試覆蓋率
- 無法知道哪些場景已測試
- 無法知道哪些場景未測試

**有場景 ID 的情況**：

```python
def generate_test_report(prd_file: str, test_results: list) -> dict:
    """
    生成測試報告
    
    Args:
        prd_file: PRD 文件路徑
        test_results: 測試結果列表
    
    Returns:
        report: 測試報告
    """
    # 1. 從 PRD 提取場景 ID
    prd_scenario_ids = extract_scenario_ids_from_prd(prd_file)
    
    # 2. 從測試結果提取場景 ID
    tested_scenario_ids = {r['scenario_id'] for r in test_results}
    
    # 3. 計算覆蓋率
    missing_scenarios = prd_scenario_ids - tested_scenario_ids
    coverage_rate = len(tested_scenario_ids & prd_scenario_ids) / len(prd_scenario_ids)
    
    # 4. 生成報告
    return {
        'coverage_rate': coverage_rate,
        'total_prd_scenarios': len(prd_scenario_ids),
        'tested_scenarios': len(tested_scenario_ids),
        'missing_scenarios': list(missing_scenarios),
        'test_results': test_results
    }
```

**優勢**：
- ✅ 可以計算場景覆蓋率
- ✅ 可以列出已測試和未測試的場景
- ✅ 可以生成詳細的測試報告
- ✅ 可以追蹤測試覆蓋率趨勢

---

### 3.3 測試維護

#### 價值 1：測試案例維護

**無場景 ID 的情況**：

```
當 PRD 場景變更時：
- 需要手動找出相關的測試案例
- 容易遺漏需要更新的測試案例
- 無法追蹤測試案例與需求的對應關係
```

**有場景 ID 的情況**：

```python
def find_tests_by_scenario_id(scenario_id: str, test_directory: str) -> list:
    """
    根據場景 ID 找出相關的測試案例
    
    Args:
        scenario_id: 場景 ID
        test_directory: 測試目錄路徑
    
    Returns:
        test_files: 相關的測試檔案列表
    """
    test_files = []
    for file_path in Path(test_directory).rglob('*.py'):
        with open(file_path, 'r', encoding='utf-8') as f:
            content = f.read()
            if scenario_id in content:
                test_files.append(str(file_path))
    
    return test_files
```

**優勢**：
- ✅ 可以快速找出相關的測試案例
- ✅ 可以追蹤測試案例與需求的對應關係
- ✅ 可以自動更新測試案例
- ✅ 減少維護成本

---

#### 價值 2：測試案例重構

**無場景 ID 的情況**：

```
當需要重構測試案例時：
- 無法知道哪些測試案例需要重構
- 無法追蹤重構對測試覆蓋率的影響
- 容易遺漏需要重構的測試案例
```

**有場景 ID 的情況**：

```python
def refactor_tests_by_scenario(scenario_id: str, new_implementation: str):
    """
    根據場景 ID 重構測試案例
    
    Args:
        scenario_id: 場景 ID
        new_implementation: 新的實作方式
    """
    # 1. 找出相關的測試案例
    test_files = find_tests_by_scenario_id(scenario_id, './tests')
    
    # 2. 更新測試案例
    for test_file in test_files:
        update_test_file(test_file, new_implementation)
    
    # 3. 驗證測試覆蓋率
    coverage = check_test_scenario_coverage(prd_file, test_directory)
    assert coverage['coverage_rate'] >= 0.9
```

**優勢**：
- ✅ 可以快速找出需要重構的測試案例
- ✅ 可以追蹤重構對測試覆蓋率的影響
- ✅ 可以自動重構測試案例
- ✅ 減少維護成本

---

## 四、實際應用範例

### 4.1 測試計劃範例

**測試計劃文件**：

```markdown
# 測試計劃：PrematchComment Feature

## 測試場景清單

### PC-LikeComment-UserLoggedIn
- **場景 ID**: PC-LikeComment-UserLoggedIn
- **場景名稱**: 使用者已登入
- **描述**: 當用戶已登入時點擊 Like 按鈕的行為
- **測試類型**: UI Automation Test
- **優先級**: P1
- **測試步驟**:
  1. 用戶已登入
  2. 用戶點擊 Like 按鈕
  3. 系統立即更新 UI（Optimistic UI），Like 數 +1
- **預期結果**: 用戶可以看到已點讚狀態，Like 數增加 1

### PC-LikeComment-UserNotLoggedIn
- **場景 ID**: PC-LikeComment-UserNotLoggedIn
- **場景名稱**: 使用者未登入
- **描述**: 當用戶未登入時點擊 Like 按鈕的行為
- **測試類型**: UI Automation Test
- **優先級**: P1
- **測試步驟**:
  1. 用戶未登入
  2. 用戶點擊 Like 按鈕
  3. 系統跳轉到登入頁面
- **預期結果**: 用戶被導向登入頁面
```

---

### 4.2 UI Automation Test 範例

**使用 Appium / Selenium**：

```python
# Test: PC-LikeComment-UserLoggedIn
# 場景 ID: PC-LikeComment-UserLoggedIn
# 場景名稱: 使用者已登入
class TestPCLikeCommentUserLoggedIn:
    """測試場景: PC-LikeComment-UserLoggedIn"""
    
    def test_like_comment_user_logged_in(self, driver, logged_in_user):
        """
        測試場景: PC-LikeComment-UserLoggedIn
        場景名稱: 使用者已登入
        描述: 當用戶已登入時點擊 Like 按鈕的行為
        """
        # Given: 用戶已登入
        driver.login(logged_in_user)
        driver.navigate_to_prematch_comment_page()
        
        # When: 用戶點擊 Like 按鈕
        like_button = driver.find_element(By.ID, "like-button")
        initial_like_count = driver.find_element(By.ID, "like-count").text
        like_button.click()
        
        # Then: 系統立即更新 UI（Optimistic UI），Like 數 +1
        new_like_count = driver.find_element(By.ID, "like-count").text
        assert int(new_like_count) == int(initial_like_count) + 1
        assert like_button.get_attribute("data-liked") == "true"
```

---

### 4.3 測試報告範例

**自動生成的測試報告**：

```json
{
  "test_report": {
    "feature": "PrematchComment",
    "total_prd_scenarios": 7,
    "tested_scenarios": 6,
    "missing_scenarios": ["PC-ReplyComment-UserNotLoggedIn"],
    "coverage_rate": 0.857,
    "test_results": [
      {
        "scenario_id": "PC-LikeComment-UserLoggedIn",
        "status": "PASS",
        "duration": 1.23,
        "test_name": "test_PC_LikeComment_UserLoggedIn"
      },
      {
        "scenario_id": "PC-LikeComment-UserNotLoggedIn",
        "status": "PASS",
        "duration": 0.89,
        "test_name": "test_PC_LikeComment_UserNotLoggedIn"
      }
    ]
  }
}
```

---

## 五、工具實作範例

### 5.1 測試覆蓋率檢查工具

**完整實作範例**：

```python
#!/usr/bin/env python3
"""
測試覆蓋率檢查工具
檢查測試是否覆蓋所有 PRD 場景
"""

import json
from pathlib import Path
from typing import Dict, List, Set

def extract_scenario_ids_from_prd(prd_file: str) -> Set[str]:
    """從 PRD 文件提取場景 ID"""
    # 實作邏輯
    pass

def extract_scenario_ids_from_test_plan(test_plan_file: str) -> Set[str]:
    """從測試計劃文件提取場景 ID"""
    # 實作邏輯
    pass

def extract_scenario_ids_from_test_results(test_results_file: str) -> Set[str]:
    """從測試結果文件提取場景 ID"""
    # 實作邏輯
    pass

def check_test_scenario_coverage(prd_file: str, test_plan_file: str, test_results_file: str) -> Dict:
    """
    檢查測試是否覆蓋所有 PRD 場景
    
    Args:
        prd_file: PRD 文件路徑
        test_plan_file: 測試計劃文件路徑
        test_results_file: 測試結果文件路徑
    
    Returns:
        coverage_report: 覆蓋率報告
    """
    # 1. 從 PRD 提取場景 ID
    prd_scenario_ids = extract_scenario_ids_from_prd(prd_file)
    
    # 2. 從測試計劃提取場景 ID
    test_plan_scenario_ids = extract_scenario_ids_from_test_plan(test_plan_file)
    
    # 3. 從測試結果提取場景 ID
    test_results_scenario_ids = extract_scenario_ids_from_test_results(test_results_file)
    
    # 4. 檢查覆蓋率
    missing_in_plan = prd_scenario_ids - test_plan_scenario_ids
    missing_in_results = prd_scenario_ids - test_results_scenario_ids
    coverage_rate = len(test_results_scenario_ids & prd_scenario_ids) / len(prd_scenario_ids) if prd_scenario_ids else 0
    
    return {
        'coverage_rate': coverage_rate,
        'missing_in_plan': list(missing_in_plan),
        'missing_in_results': list(missing_in_results),
        'total_prd_scenarios': len(prd_scenario_ids),
        'total_test_plan_scenarios': len(test_plan_scenario_ids),
        'total_test_results_scenarios': len(test_results_scenario_ids)
    }

if __name__ == '__main__':
    import sys
    
    if len(sys.argv) < 4:
        print("Usage: python check_test_coverage.py <prd_file> <test_plan_file> <test_results_file>")
        sys.exit(1)
    
    prd_file = sys.argv[1]
    test_plan_file = sys.argv[2]
    test_results_file = sys.argv[3]
    
    report = check_test_scenario_coverage(prd_file, test_plan_file, test_results_file)
    print(json.dumps(report, indent=2, ensure_ascii=False))
```

---

## 六、總結

### 6.1 場景覆蓋率檢查工具的使用時機

| 階段 | 角色 | 使用時機 | 檢查內容 |
|------|------|---------|---------|
| TDD 生成階段 | Client Side RD | TDD 生成後 | PRD 場景 vs TDD 測試場景 |
| 開發與測試階段 | Client Side RD | 開發完成後 | PRD 場景 vs 程式碼場景 ID |
| 測試階段 | QA / Test Engineer | 測試計劃制定時 | PRD 場景 vs 測試計劃 |
| 測試階段 | QA / Test Engineer | 測試執行前 | PRD 場景 vs 測試案例 |
| 測試階段 | QA / Test Engineer | 測試執行後 | PRD 場景 vs 測試結果 |
| 驗收階段 | PM / QA | 功能驗收時 | PRD 場景 vs 驗收結果 |

---

### 6.2 場景 ID 對 QA 的價值

1. **測試計劃制定**
   - 系統化測試計劃
   - 測試案例管理

2. **測試執行與追蹤**
   - 測試結果追蹤
   - Bug 追蹤

3. **測試報告生成**
   - 自動化測試報告
   - 場景覆蓋率計算

---

### 6.3 場景 ID 對 UI Automation Test 的價值

1. **測試案例撰寫**
   - 測試案例結構化
   - 測試案例自動生成

2. **測試執行與報告**
   - 測試結果對應
   - 測試報告自動生成

3. **測試維護**
   - 測試案例維護
   - 測試案例重構

---

## 參考資料

- [場景 ID 與 Codebase 連結規範](./SCENARIO_ID_CODEBASE_LINKING.md)
- [場景 ID 語義化命名規則](./SCENARIO_ID_NAMING_RULES.md)
- [場景 ID 痛點分析：溝通與文件實作](./SCENARIO_ID_PAIN_POINTS_ANALYSIS.md)

