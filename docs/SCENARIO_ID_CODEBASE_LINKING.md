# 場景 ID 與 Codebase 連結規範

## 概述

本文件說明如何將場景 ID 與 codebase 連結，建立從 PRD → TDD → Code 的完整追蹤鏈。

---

## 一、連結方式

### 1.1 程式碼註解標註

#### Swift 程式碼範例

**UseCase 實作**：

```swift
// MARK: - Scenario: PC-LikeComment-UserLoggedIn
// 場景 ID: PC-LikeComment-UserLoggedIn
// 場景名稱: 使用者已登入
// 描述: 當用戶已登入時點擊 Like 按鈕的行為
// 對應 PRD 場景: PC-LikeComment-UserLoggedIn
// 對應 TDD Flow: PC-SUB-004
struct ToggleLikeUseCase {
    func execute(commentId: String) async throws {
        // 實作邏輯
    }
}
```

**Feature 實作**：

```swift
// MARK: - Scenario: PC-LikeComment-UserLoggedIn
// 場景 ID: PC-LikeComment-UserLoggedIn
struct PrematchCommentFeature: Reducer {
    func reduce(into state: inout State, action: Action) -> Effect<Action> {
        switch action {
        case .toggleLike(let commentId):
            // 場景 ID: PC-LikeComment-UserLoggedIn
            return .run { send in
                // 實作邏輯
            }
        }
    }
}
```

**Repository 實作**：

```swift
// MARK: - Scenario: PC-LikeComment-UserLoggedIn
// 場景 ID: PC-LikeComment-UserLoggedIn
protocol PrematchCommentRepository {
    // 場景 ID: PC-LikeComment-UserLoggedIn
    func toggleLike(commentId: String) async throws -> Comment
}
```

---

#### Python 程式碼範例

**UseCase 實作**：

```python
# Scenario: PC-LikeComment-UserLoggedIn
# 場景 ID: PC-LikeComment-UserLoggedIn
# 場景名稱: 使用者已登入
# 描述: 當用戶已登入時點擊 Like 按鈕的行為
# 對應 PRD 場景: PC-LikeComment-UserLoggedIn
# 對應 TDD Flow: PC-SUB-004
class ToggleLikeUseCase:
    def execute(self, comment_id: str) -> Comment:
        # 實作邏輯
        pass
```

---

### 1.2 測試案例標註

#### Swift 測試範例

```swift
// MARK: - Test: PC-LikeComment-UserLoggedIn
// 場景 ID: PC-LikeComment-UserLoggedIn
// 場景名稱: 使用者已登入
func testToggleLike_UserLoggedIn() async throws {
    // Given: 用戶已登入
    // When: 用戶點擊 Like 按鈕
    // Then: 系統立即更新 UI（Optimistic UI），Like 數 +1
}
```

---

#### Python 測試範例

```python
# Test: PC-LikeComment-UserLoggedIn
# 場景 ID: PC-LikeComment-UserLoggedIn
# 場景名稱: 使用者已登入
def test_toggle_like_user_logged_in():
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

---

### 1.3 常數定義

#### Swift 常數範例

```swift
// MARK: - Scenario IDs
enum ScenarioID {
    // PrematchComment Feature
    static let likeCommentUserLoggedIn = "PC-LikeComment-UserLoggedIn"
    static let likeCommentUserNotLoggedIn = "PC-LikeComment-UserNotLoggedIn"
    static let publishCommentUserLoggedInWithNickname = "PC-PublishComment-UserLoggedInWithNickname"
    
    // LiveChat Feature
    static let sendMessageUserLoggedIn = "LC-SendMessage-UserLoggedIn"
    static let sendMessageUserNotLoggedIn = "LC-SendMessage-UserNotLoggedIn"
}
```

**使用方式**：

```swift
// 場景 ID: ScenarioID.likeCommentUserLoggedIn
func toggleLike(commentId: String) async throws {
    // 實作邏輯
}
```

---

#### Python 常數範例

```python
# Scenario IDs
class ScenarioID:
    # PrematchComment Feature
    LIKE_COMMENT_USER_LOGGED_IN = "PC-LikeComment-UserLoggedIn"
    LIKE_COMMENT_USER_NOT_LOGGED_IN = "PC-LikeComment-UserNotLoggedIn"
    PUBLISH_COMMENT_USER_LOGGED_IN_WITH_NICKNAME = "PC-PublishComment-UserLoggedInWithNickname"
    
    # LiveChat Feature
    SEND_MESSAGE_USER_LOGGED_IN = "LC-SendMessage-UserLoggedIn"
    SEND_MESSAGE_USER_NOT_LOGGED_IN = "LC-SendMessage-UserNotLoggedIn"
```

---

### 1.4 文件標註

#### README 或文件標註

```markdown
## 實作場景

### PC-LikeComment-UserLoggedIn

**場景 ID**: `PC-LikeComment-UserLoggedIn`  
**場景名稱**: 使用者已登入  
**描述**: 當用戶已登入時點擊 Like 按鈕的行為

**相關檔案**:
- `PrematchCommentFeature.swift` (Line 45-67)
- `ToggleLikeUseCase.swift` (Line 12-34)
- `PrematchCommentRepository.swift` (Line 89-102)
- `ToggleLikeUseCaseTests.swift` (Line 23-45)

**對應 PRD**: [PRD 連結]  
**對應 TDD**: [TDD 連結]
```

---

## 二、自動化工具

### 2.1 場景 ID 提取工具

**功能**：從 codebase 中提取所有場景 ID

```python
import re
from pathlib import Path

def extract_scenario_ids_from_codebase(codebase_path: str) -> dict:
    """
    從 codebase 中提取所有場景 ID
    
    Args:
        codebase_path: codebase 根目錄路徑
    
    Returns:
        scenario_mapping: 場景 ID 與程式碼的對應關係
    """
    scenario_mapping = {}
    pattern = r'場景 ID[：:]\s*([A-Z]+-[A-Za-z]+-[A-Za-z]+)'
    
    for file_path in Path(codebase_path).rglob('*.swift'):
        with open(file_path, 'r', encoding='utf-8') as f:
            content = f.read()
            matches = re.findall(pattern, content)
            for scenario_id in matches:
                if scenario_id not in scenario_mapping:
                    scenario_mapping[scenario_id] = []
                scenario_mapping[scenario_id].append({
                    'file': str(file_path),
                    'line': content[:content.find(scenario_id)].count('\n') + 1
                })
    
    return scenario_mapping
```

---

### 2.2 場景覆蓋率檢查工具

**功能**：檢查 codebase 是否覆蓋所有 PRD 場景

```python
def check_scenario_coverage(prd_file: str, codebase_path: str) -> dict:
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

---

### 2.3 場景對應關係建立工具

**功能**：建立 PRD → TDD → Code 的對應關係

```python
def establish_scenario_mapping(prd_file: str, tdd_file: str, codebase_path: str) -> dict:
    """
    建立 PRD → TDD → Code 的對應關係
    
    Args:
        prd_file: PRD 文件路徑
        tdd_file: TDD 文件路徑
        codebase_path: codebase 根目錄路徑
    
    Returns:
        scenario_mapping: 完整的場景對應關係
    """
    # 1. 從 PRD 提取場景 ID
    prd_scenarios = extract_scenarios_from_prd(prd_file)
    
    # 2. 從 TDD 提取場景 ID
    tdd_scenarios = extract_scenarios_from_tdd(tdd_file)
    
    # 3. 從 codebase 提取場景 ID
    codebase_scenarios = extract_scenario_ids_from_codebase(codebase_path)
    
    # 4. 建立對應關係
    scenario_mapping = {}
    for scenario_id in prd_scenarios:
        scenario_mapping[scenario_id] = {
            'prd': prd_scenarios[scenario_id],
            'tdd': tdd_scenarios.get(scenario_id),
            'code': codebase_scenarios.get(scenario_id, [])
        }
    
    return scenario_mapping
```

---

### 2.4 場景 ID 驗證工具

**功能**：驗證 codebase 中的場景 ID 是否符合命名規範

```python
import re

def validate_scenario_id_format(scenario_id: str) -> bool:
    """
    驗證場景 ID 是否符合命名規範
    
    Args:
        scenario_id: 場景 ID
    
    Returns:
        is_valid: 是否符合規範
    """
    pattern = r'^[A-Z]{2,3}-[A-Z][a-zA-Z]+-[A-Z][a-zA-Z]+$'
    return bool(re.match(pattern, scenario_id))

def validate_codebase_scenario_ids(codebase_path: str) -> dict:
    """
    驗證 codebase 中所有場景 ID 是否符合命名規範
    
    Args:
        codebase_path: codebase 根目錄路徑
    
    Returns:
        validation_report: 驗證報告
    """
    codebase_scenarios = extract_scenario_ids_from_codebase(codebase_path)
    invalid_scenarios = []
    
    for scenario_id in codebase_scenarios:
        if not validate_scenario_id_format(scenario_id):
            invalid_scenarios.append({
                'scenario_id': scenario_id,
                'files': codebase_scenarios[scenario_id]
            })
    
    return {
        'total_scenarios': len(codebase_scenarios),
        'invalid_scenarios': invalid_scenarios,
        'valid_count': len(codebase_scenarios) - len(invalid_scenarios),
        'invalid_count': len(invalid_scenarios)
    }
```

---

## 三、IDE 整合

### 3.1 VS Code 擴充功能

**功能**：
- 場景 ID 自動補全
- 場景 ID 跳轉到 PRD/TDD
- 場景 ID 驗證

**設定範例**：

```json
{
  "scenarioId.autoComplete": true,
  "scenarioId.validation": true,
  "scenarioId.prdPath": "./docs/PRD",
  "scenarioId.tddPath": "./output"
}
```

---

### 3.2 Xcode 擴充功能

**功能**：
- 場景 ID 自動補全
- 場景 ID 跳轉到 PRD/TDD
- 場景 ID 驗證

**設定範例**：

```swift
// Xcode Source Editor Extension
class ScenarioIDExtension: SourceEditorExtension {
    func perform(with invocation: XCSourceEditorCommandInvocation) {
        // 實作場景 ID 相關功能
    }
}
```

---

## 四、CI/CD 整合

### 4.1 GitHub Actions 範例

**功能**：在 CI/CD 流程中檢查場景 ID 覆蓋率

```yaml
name: Scenario ID Coverage Check

on:
  pull_request:
    branches: [main]

jobs:
  check-scenario-coverage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Check Scenario ID Coverage
        run: |
          python scripts/check_scenario_coverage.py \
            --prd ./docs/PRD \
            --codebase ./src \
            --output coverage_report.json
      
      - name: Upload Coverage Report
        uses: actions/upload-artifact@v3
        with:
          name: scenario-coverage-report
          path: coverage_report.json
```

---

### 4.2 Git Hooks

**功能**：在 commit 前檢查場景 ID 格式

```bash
#!/bin/bash
# .git/hooks/pre-commit

python scripts/validate_scenario_ids.py --codebase .

if [ $? -ne 0 ]; then
    echo "❌ Scenario ID validation failed. Please fix the issues."
    exit 1
fi
```

---

## 五、最佳實踐建議

### 5.1 標註原則

1. **關鍵位置標註**
   - UseCase 實作：標註主要業務邏輯
   - Feature 實作：標註 Action 處理
   - Repository 實作：標註資料存取邏輯
   - 測試案例：標註對應的場景 ID

2. **標註格式統一**
   - 使用統一的註解格式
   - 包含場景 ID、場景名稱、描述
   - 包含對應的 PRD/TDD 連結

3. **避免過度標註**
   - 只在關鍵位置標註
   - 避免在每個函數都標註
   - 優先標註業務邏輯相關的程式碼

---

### 5.2 工具使用

1. **自動化檢查**
   - 在 CI/CD 流程中加入場景 ID 檢查
   - 使用 Git Hooks 在 commit 前檢查
   - 定期執行場景覆蓋率檢查

2. **IDE 整合**
   - 使用 IDE 擴充功能自動補全場景 ID
   - 使用 IDE 擴充功能跳轉到 PRD/TDD
   - 使用 IDE 擴充功能驗證場景 ID

---

### 5.3 維護原則

1. **同步更新**
   - 當場景 ID 變更時，同步更新程式碼註解
   - 當程式碼變更時，檢查場景 ID 是否仍然正確
   - 定期檢查場景 ID 與程式碼的一致性

2. **文檔維護**
   - 維護場景 ID 與程式碼的對照表
   - 更新 README 或文件中的場景 ID 連結
   - 定期檢查場景 ID 的完整性

---

## 六、實際應用範例

### 6.1 完整範例：PrematchComment Feature

**PRD 場景**：

```yaml
scenarios:
  - scenario_id: "PC-LikeComment-UserLoggedIn"
    name: "使用者已登入"
    description: "當用戶已登入時點擊 Like 按鈕的行為"
```

**TDD 場景**：

```markdown
### Scenario: PC-LikeComment-UserLoggedIn - 使用者已登入
**對應 PRD 場景**: PC-LikeComment-UserLoggedIn
**對應 Flow**: PC-SUB-004
```

**程式碼實作**：

```swift
// MARK: - Scenario: PC-LikeComment-UserLoggedIn
// 場景 ID: PC-LikeComment-UserLoggedIn
// 場景名稱: 使用者已登入
// 描述: 當用戶已登入時點擊 Like 按鈕的行為
// 對應 PRD 場景: PC-LikeComment-UserLoggedIn
// 對應 TDD Flow: PC-SUB-004
struct ToggleLikeUseCase {
    func execute(commentId: String) async throws -> Comment {
        // 實作邏輯
    }
}
```

**測試案例**：

```swift
// MARK: - Test: PC-LikeComment-UserLoggedIn
// 場景 ID: PC-LikeComment-UserLoggedIn
func testToggleLike_UserLoggedIn() async throws {
    // 測試邏輯
}
```

---

### 6.2 場景對應關係報告

**自動生成的報告範例**：

```json
{
  "PC-LikeComment-UserLoggedIn": {
    "prd": {
      "file": "docs/PRD/prematch_comment.yaml",
      "line": 217
    },
    "tdd": {
      "file": "output/PrematchComment/10_Test Scenarios/01_test_scenarios.md",
      "line": 45
    },
    "code": [
      {
        "file": "src/PrematchComment/ToggleLikeUseCase.swift",
        "line": 12
      },
      {
        "file": "src/PrematchComment/PrematchCommentFeature.swift",
        "line": 45
      },
      {
        "file": "tests/PrematchComment/ToggleLikeUseCaseTests.swift",
        "line": 23
      }
    ]
  }
}
```

---

## 七、工具實作範例

### 7.1 場景 ID 提取腳本

**完整實作範例**：

```python
#!/usr/bin/env python3
"""
場景 ID 提取工具
從 codebase 中提取所有場景 ID 並生成對照表
"""

import re
import json
from pathlib import Path
from typing import Dict, List

def extract_scenario_ids_from_file(file_path: Path) -> List[Dict]:
    """
    從單一檔案中提取場景 ID
    
    Args:
        file_path: 檔案路徑
    
    Returns:
        scenarios: 場景 ID 列表
    """
    scenarios = []
    patterns = [
        r'場景 ID[：:]\s*([A-Z]+-[A-Za-z]+-[A-Za-z]+)',
        r'Scenario[：:]\s*([A-Z]+-[A-Za-z]+-[A-Za-z]+)',
        r'ScenarioID\.([a-zA-Z]+)',  # 常數定義
    ]
    
    try:
        with open(file_path, 'r', encoding='utf-8') as f:
            content = f.read()
            lines = content.split('\n')
            
            for line_num, line in enumerate(lines, 1):
                for pattern in patterns:
                    matches = re.finditer(pattern, line)
                    for match in matches:
                        scenario_id = match.group(1)
                        # 如果是常數定義，需要轉換為完整場景 ID
                        if 'ScenarioID.' in line:
                            scenario_id = convert_constant_to_scenario_id(scenario_id)
                        
                        scenarios.append({
                            'scenario_id': scenario_id,
                            'file': str(file_path),
                            'line': line_num,
                            'context': line.strip()
                        })
    except Exception as e:
        print(f"Error reading {file_path}: {e}")
    
    return scenarios

def extract_scenario_ids_from_codebase(codebase_path: str) -> Dict[str, List[Dict]]:
    """
    從 codebase 中提取所有場景 ID
    
    Args:
        codebase_path: codebase 根目錄路徑
    
    Returns:
        scenario_mapping: 場景 ID 與程式碼的對應關係
    """
    scenario_mapping = {}
    codebase = Path(codebase_path)
    
    # 支援的檔案類型
    supported_extensions = ['.swift', '.py', '.ts', '.js', '.java', '.kt']
    
    for file_path in codebase.rglob('*'):
        if file_path.suffix in supported_extensions:
            scenarios = extract_scenario_ids_from_file(file_path)
            for scenario in scenarios:
                scenario_id = scenario['scenario_id']
                if scenario_id not in scenario_mapping:
                    scenario_mapping[scenario_id] = []
                scenario_mapping[scenario_id].append({
                    'file': scenario['file'],
                    'line': scenario['line'],
                    'context': scenario['context']
                })
    
    return scenario_mapping

def generate_mapping_report(scenario_mapping: Dict, output_path: str):
    """
    生成場景 ID 對照表報告
    
    Args:
        scenario_mapping: 場景 ID 與程式碼的對應關係
        output_path: 輸出檔案路徑
    """
    report = {
        'total_scenarios': len(scenario_mapping),
        'scenarios': scenario_mapping
    }
    
    with open(output_path, 'w', encoding='utf-8') as f:
        json.dump(report, f, indent=2, ensure_ascii=False)
    
    print(f"✅ 場景 ID 對照表已生成: {output_path}")

if __name__ == '__main__':
    import sys
    
    if len(sys.argv) < 2:
        print("Usage: python extract_scenario_ids.py <codebase_path> [output_path]")
        sys.exit(1)
    
    codebase_path = sys.argv[1]
    output_path = sys.argv[2] if len(sys.argv) > 2 else 'scenario_mapping.json'
    
    scenario_mapping = extract_scenario_ids_from_codebase(codebase_path)
    generate_mapping_report(scenario_mapping, output_path)
```

---

## 八、總結

### 核心價值

1. **完整追蹤鏈**：建立 PRD → TDD → Code 的完整追蹤鏈
2. **自動化檢查**：可以自動檢查場景覆蓋率和一致性
3. **易於維護**：場景 ID 變更時可以自動更新相關程式碼
4. **提高效率**：減少人工對照和檢查的時間

### 實施建議

1. **階段性實施**
   - 階段 1：在關鍵位置標註場景 ID
   - 階段 2：開發自動化工具
   - 階段 3：整合到 CI/CD 流程

2. **工具支援**
   - 開發場景 ID 提取工具
   - 開發場景覆蓋率檢查工具
   - 開發場景對應關係建立工具

3. **團隊協作**
   - 建立場景 ID 標註規範
   - 培訓團隊使用場景 ID
   - 定期檢查場景 ID 的一致性

---

## 參考資料

- [場景 ID 語義化命名規則](./SCENARIO_ID_NAMING_RULES.md)
- [場景 ID 痛點分析：溝通與文件實作](./SCENARIO_ID_PAIN_POINTS_ANALYSIS.md)
- [場景 ID 跨 Feature 處理規範](./SCENARIO_ID_CROSS_FEATURE.md)

