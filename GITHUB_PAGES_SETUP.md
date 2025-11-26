# GitHub Pages 獨立部署設置說明

## 重要說明

由於 GitHub Pages 的限制，同一個 repository 通常只能有一個主要的 GitHub Pages 部署。要實現獨立的 Scenario ID 文件集部署，有以下幾種方案：

---

## 方案 1：使用不同的 GitHub Repository（推薦）

**優點**：
- ✅ 完全獨立，不會互相影響
- ✅ 可以有不同的部署 URL
- ✅ 管理簡單

**步驟**：

1. 創建新的 GitHub repository（例如：`scenario-id-docs`）
2. 將 `scenario_id_docs/` 目錄的內容推送到新 repository
3. 在新 repository 中設置 GitHub Pages
4. 部署 URL：`https://{username}.github.io/scenario-id-docs/`

---

## 方案 2：使用不同的 Branch（備用方案）

**優點**：
- ✅ 在同一個 repository 中
- ✅ 可以獨立管理

**缺點**：
- ❌ 需要維護多個 branch
- ❌ 部署 URL 可能會有衝突

**步驟**：

1. 創建新的 branch：`gh-pages-scenario-id`
2. 修改 workflow 部署到不同的 branch
3. 在 GitHub Pages 設置中選擇不同的 branch

---

## 方案 3：使用 GitHub Pages 子路徑（需要特殊配置）

**注意**：此方案需要修改 `mkdocs.yml` 配置。

**步驟**：

1. 修改 `mkdocs.yml`，添加 `site_url` 和 `use_directory_urls`：

```yaml
site_url: https://{username}.github.io/{repo-name}/
use_directory_urls: true
```

2. 部署到子路徑：`/scenario-id-docs/`

**缺點**：
- ❌ 需要手動配置 base URL
- ❌ 可能與主 GitHub Pages 衝突

---

## 推薦方案：方案 1（獨立 Repository）

### 步驟詳解

#### 1. 創建新 Repository

1. 前往 GitHub
2. 點擊 "New repository"
3. Repository name: `scenario-id-docs`
4. 選擇 Public 或 Private
5. 點擊 "Create repository"

#### 2. 推送文件到新 Repository

```bash
# 1. 進入 scenario_id_docs 目錄
cd scenario_id_docs

# 2. 初始化 Git（如果還沒有）
git init

# 3. 添加所有文件
git add .

# 4. Commit
git commit -m "Initial commit: Scenario ID documentation"

# 5. 添加新 repository 為 remote
git remote add origin https://github.com/{username}/scenario-id-docs.git

# 6. Push 到新 repository
git branch -M main
git push -u origin main
```

#### 3. 設置 GitHub Pages

1. 前往新 repository 的 Settings → Pages
2. Source 選擇 "GitHub Actions"
3. 創建 GitHub Environment：`github-pages`
4. 部署 URL：`https://{username}.github.io/scenario-id-docs/`

#### 4. 更新 Workflow

在新 repository 中創建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy Scenario ID Docs to GitHub Pages

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.x'
      
      - name: Install dependencies
        run: |
          pip install mkdocs-material
          pip install pymdown-extensions
          pip install mkdocs-minify-plugin
      
      - name: Setup Pages
        uses: actions/configure-pages@v4
      
      - name: Build with MkDocs
        run: |
          mkdocs build --verbose
      
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: site
      
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

**注意**：在新 repository 中，`mkdocs.yml` 和 `docs/` 應該在根目錄。

---

## 方案 4：保持在同一 Repository（當前配置）

如果選擇保持在同一 repository，需要：

1. **確保使用不同的 Environment**：
   - TDD 文件：`github-pages`
   - Scenario ID 文件：`github-pages-scenario-id-docs`

2. **注意部署衝突**：
   - 兩個 workflow 可能會互相覆蓋
   - 建議使用不同的 branch 或不同的 repository

---

## 當前配置說明

目前的配置（`.github/workflows/deploy-scenario-id-docs.yml`）會：

1. 監聽 `scenario_id_docs/**` 的變更
2. 使用獨立的 Environment：`github-pages-scenario-id-docs`
3. 部署到 GitHub Pages

**潛在問題**：
- 如果 TDD 文件和 Scenario ID 文件都部署到同一個 GitHub Pages，可能會互相覆蓋
- 建議使用方案 1（獨立 Repository）或方案 2（不同 Branch）

---

## 建議

**強烈建議使用方案 1（獨立 Repository）**，因為：

1. ✅ 完全獨立，不會互相影響
2. ✅ 可以有不同的部署 URL
3. ✅ 管理簡單
4. ✅ 不會有部署衝突

---

## 快速遷移到獨立 Repository

如果選擇方案 1，可以使用以下腳本快速遷移：

```bash
#!/bin/bash
# 遷移腳本

# 1. 創建新 repository（手動在 GitHub 創建）

# 2. 複製文件到新目錄
mkdir -p ../scenario-id-docs
cp -r scenario_id_docs/* ../scenario-id-docs/

# 3. 在新目錄中初始化 Git
cd ../scenario-id-docs
git init
git add .
git commit -m "Initial commit: Scenario ID documentation"

# 4. 添加 remote（替換 {username}）
git remote add origin https://github.com/{username}/scenario-id-docs.git
git branch -M main
git push -u origin main
```

---

## 相關文件

- [DEPLOYMENT_GUIDE.md](./DEPLOYMENT_GUIDE.md) - 詳細部署指南
- [SETUP.md](./SETUP.md) - 設置指南

---

**最後更新**：2024-12-19

