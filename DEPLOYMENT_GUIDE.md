# Scenario ID 文件集部署指南

## 概述

本指南說明如何將 Scenario ID 文件集部署到獨立的 GitHub Pages。

---

## 前置需求

- Python 3.x
- Git
- GitHub repository 的寫入權限

---

## 步驟 1：安裝依賴

### 方法 1：使用 requirements.txt（推薦）

```bash
cd scenario_id_docs
pip install -r requirements.txt
```

### 方法 2：手動安裝

```bash
pip install mkdocs-material
pip install pymdown-extensions
pip install mkdocs-minify-plugin
```

---

## 步驟 2：本地測試

### 預覽網站

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

生成的網站會在 `site/` 目錄下。

**注意**：`site/` 目錄已在 `.gitignore` 中，不會被提交到 Git。

---

## 步驟 3：GitHub Pages 設置

### 3.1 創建 GitHub Environment

1. 前往 GitHub repository
2. 前往 **Settings** → **Environments**
3. 點擊 **New environment**
4. 輸入環境名稱：`github-pages-scenario-id-docs`
5. 點擊 **Configure environment**
6. 在 **Deployment branches** 選擇 **Selected branches**，選擇 `main` 分支
7. 點擊 **Save protection rules**

---

### 3.2 啟用 GitHub Pages

1. 前往 **Settings** → **Pages**
2. 在 **Build and deployment** 區塊：
   - **Source** 選擇 **GitHub Actions**
3. 確認 workflow 文件 `.github/workflows/deploy-scenario-id-docs.yml` 已存在

---

## 步驟 4：部署

### 自動部署（推薦）

當以下情況發生時，GitHub Actions 會自動部署：

1. **Push 到 main 分支**且修改了以下路徑：
   - `scenario_id_docs/**`
   - `.github/workflows/deploy-scenario-id-docs.yml`

2. **手動觸發**：
   - 前往 **Actions** 標籤頁
   - 選擇 **Deploy Scenario ID Docs to GitHub Pages**
   - 點擊 **Run workflow**

---

### 手動部署（備用方案）

如果需要手動部署：

```bash
cd scenario_id_docs
mkdocs gh-deploy
```

**注意**：手動部署會覆蓋自動部署，建議使用自動部署。

---

## 步驟 5：驗證部署

1. Push 到 `main` 分支
2. 前往 **Actions** 標籤頁
3. 查看 **Deploy Scenario ID Docs to GitHub Pages** workflow
4. 等待部署完成（通常需要 1-2 分鐘）
5. 前往 **Settings** → **Pages** 查看部署 URL

---

## 部署 URL

部署完成後，文件集會發布到：

```
https://{username}.github.io/{repo-name}/scenario-id-docs/
```

**範例**：
- Repository: `MermaidToTDD`
- Username: `your-username`
- URL: `https://your-username.github.io/MermaidToTDD/scenario-id-docs/`

---

## 獨立部署說明

### 與 TDD 文件分開

- **TDD 文件**：部署到 `https://{username}.github.io/{repo-name}/`
- **Scenario ID 文件**：部署到 `https://{username}.github.io/{repo-name}/scenario-id-docs/`

兩者使用不同的：
- GitHub Environment（`github-pages` vs `github-pages-scenario-id-docs`）
- GitHub Actions Workflow（`deploy.yml` vs `deploy-scenario-id-docs.yml`）
- 部署路徑（`/` vs `/scenario-id-docs/`）

---

## 文件結構

```
scenario_id_docs/
├── README.md                    # 文件集說明
├── SETUP.md                     # 設置指南
├── DEPLOYMENT_GUIDE.md          # 部署指南（本文件）
├── mkdocs.yml                   # MkDocs 配置
├── requirements.txt             # Python 依賴
└── docs/                        # 文件目錄
    ├── index.md                 # 首頁
    ├── SCENARIO_ID_PROPOSAL.md
    ├── SCENARIO_ID_PAIN_POINTS_ANALYSIS.md
    ├── SCENARIO_ID_NAMING_RULES.md
    ├── SCENARIO_ID_SOLUTION.md
    ├── SCENARIO_ID_CROSS_FEATURE.md
    ├── SCENARIO_ID_CODEBASE_LINKING.md
    └── SCENARIO_ID_QA_AND_TESTING.md
```

---

## 故障排除

### 問題 1：部署失敗

**錯誤訊息**：`Environment "github-pages-scenario-id-docs" not found`

**解決方案**：
1. 前往 **Settings** → **Environments**
2. 創建名為 `github-pages-scenario-id-docs` 的環境
3. 重新執行 workflow

---

### 問題 2：文件無法顯示

**錯誤訊息**：`404 Not Found`

**檢查項目**：
- 確認文件路徑正確
- 確認 `mkdocs.yml` 中的 `nav` 配置正確
- 檢查文件是否在 `docs/` 目錄下

**解決方案**：
```bash
# 本地測試建置
cd scenario_id_docs
mkdocs build --verbose
```

---

### 問題 3：Mermaid 圖表無法顯示

**解決方案**：
```bash
# 重新安裝依賴
pip install --upgrade pymdown-extensions
```

---

### 問題 4：Workflow 沒有觸發

**檢查項目**：
- 確認修改的文件路徑符合觸發條件
- 確認 workflow 文件路徑正確：`.github/workflows/deploy-scenario-id-docs.yml`

**解決方案**：
- 手動觸發 workflow
- 或修改 `scenario_id_docs/` 目錄下的任何文件後 Push

---

## 更新文件

### 新增文件

1. 在 `docs/` 目錄下新增 Markdown 文件
2. 更新 `mkdocs.yml` 中的 `nav` 配置
3. Commit 並 Push

**範例**：

```bash
# 1. 新增文件
vim docs/SCENARIO_ID_NEW_DOC.md

# 2. 更新 mkdocs.yml（在 nav 區塊新增）
nav:
  - 新文件: SCENARIO_ID_NEW_DOC.md

# 3. Commit 並 Push
git add .
git commit -m "Add new scenario ID document"
git push
```

---

### 修改文件

1. 直接編輯 `docs/` 目錄下的文件
2. Commit 並 Push
3. GitHub Actions 會自動部署

---

## 本地開發工作流程

```bash
# 1. 編輯文件
vim docs/SCENARIO_ID_PROPOSAL.md

# 2. 本地預覽
cd scenario_id_docs
mkdocs serve

# 3. 檢查建置
mkdocs build

# 4. Commit 並 Push
cd ..
git add .
git commit -m "Update scenario ID documentation"
git push
```

---

## 檢查清單

部署前請確認：

- [ ] Python 依賴已安裝
- [ ] 本地預覽正常（`mkdocs serve`）
- [ ] 本地建置成功（`mkdocs build`）
- [ ] GitHub Environment 已創建（`github-pages-scenario-id-docs`）
- [ ] GitHub Pages 已啟用（Settings → Pages）
- [ ] Workflow 文件已存在（`.github/workflows/deploy-scenario-id-docs.yml`）
- [ ] 所有文件都在 `docs/` 目錄下
- [ ] `mkdocs.yml` 中的 `nav` 配置正確

---

## 相關文件

- [README.md](./README.md) - 文件集說明
- [SETUP.md](./SETUP.md) - 設置指南
- [MkDocs 設定說明](../MKDOCS_SETUP.md) - 原始 MkDocs 設置說明

---

**最後更新**：2024-12-19

