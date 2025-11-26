# Scenario ID 文件集設置指南

## 概述

本指南說明如何設置和部署 Scenario ID 文件集到獨立的 GitHub Pages。

---

## 前置需求

- Python 3.x
- Git
- GitHub repository

---

## 設置步驟

### 1. 安裝 Python 依賴

```bash
cd scenario_id_docs
pip install -r requirements.txt
```

或手動安裝：

```bash
pip install mkdocs-material
pip install pymdown-extensions
pip install mkdocs-minify-plugin
```

---

### 2. 本地預覽

```bash
cd scenario_id_docs
mkdocs serve
```

然後在瀏覽器中開啟 `http://127.0.0.1:8000` 即可預覽。

---

### 3. 建置網站

```bash
cd scenario_id_docs
mkdocs build
```

生成的網站會在 `site/` 目錄下。

---

## GitHub Pages 部署設置

### 步驟 1：創建 GitHub Environment

1. 前往 GitHub repository
2. 前往 Settings → Environments
3. 點擊 "New environment"
4. 輸入環境名稱：`github-pages-scenario-id-docs`
5. 點擊 "Configure environment"
6. 在 "Deployment branches" 選擇 "Selected branches"，選擇 `main` 分支
7. 點擊 "Save protection rules"

---

### 步驟 2：啟用 GitHub Pages

1. 前往 Settings → Pages
2. 在 "Build and deployment" 區塊：
   - Source 選擇 "GitHub Actions"
3. 確保 workflow 文件 `.github/workflows/deploy-scenario-id-docs.yml` 已存在

---

### 步驟 3：驗證部署

1. Push 到 `main` 分支
2. 前往 Actions 標籤頁
3. 查看 "Deploy Scenario ID Docs to GitHub Pages" workflow
4. 等待部署完成
5. 前往 Settings → Pages 查看部署 URL

---

## 部署 URL

部署完成後，文件集會發布到：

```
https://{username}.github.io/{repo-name}/scenario-id-docs/
```

**注意**：
- 這是獨立的 GitHub Pages 部署
- 與 TDD 文件分開，不會互相影響
- URL 路徑為 `/scenario-id-docs/`

---

## 自動部署觸發條件

Workflow 會在以下情況自動觸發：

1. **Push 到 main 分支**且修改了以下路徑：
   - `scenario_id_docs/**`
   - `.github/workflows/deploy-scenario-id-docs.yml`

2. **手動觸發**：
   - 前往 Actions 標籤頁
   - 選擇 "Deploy Scenario ID Docs to GitHub Pages"
   - 點擊 "Run workflow"

---

## 故障排除

### 問題 1：部署失敗

**檢查項目**：
- 確認 GitHub Environment 已創建
- 確認 GitHub Pages 已啟用
- 檢查 workflow 日誌中的錯誤訊息

**解決方案**：
```bash
# 本地測試建置
cd scenario_id_docs
mkdocs build --verbose
```

---

### 問題 2：文件無法顯示

**檢查項目**：
- 確認文件路徑正確
- 確認 `mkdocs.yml` 中的 `nav` 配置正確
- 檢查文件是否在 `docs/` 目錄下

**解決方案**：
```bash
# 檢查文件結構
cd scenario_id_docs
ls -la docs/
```

---

### 問題 3：Mermaid 圖表無法顯示

**檢查項目**：
- 確認已安裝 `pymdown-extensions`
- 確認 `mkdocs.yml` 中已配置 Mermaid 支援

**解決方案**：
```bash
# 重新安裝依賴
pip install --upgrade pymdown-extensions
```

---

## 更新文件

### 新增文件

1. 在 `docs/` 目錄下新增 Markdown 文件
2. 更新 `mkdocs.yml` 中的 `nav` 配置
3. Commit 並 Push

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
mkdocs serve

# 3. 檢查建置
mkdocs build

# 4. Commit 並 Push
git add .
git commit -m "Update scenario ID documentation"
git push
```

---

## 相關文件

- [MkDocs 設定說明](../MKDOCS_SETUP.md)
- [Git 使用指南](../docs/GIT_USAGE.md)

---

**最後更新**：2024-12-19

