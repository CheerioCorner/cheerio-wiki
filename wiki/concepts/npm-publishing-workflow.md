---
title: Package Publishing Workflow — 統一的發佈流程觀點
type: concept
created: 2026-07-31
updated: 2026-07-31
sources: 1
tags: [publishing, package, extension, marketplace, ci-cd, npm, nuget, distribution]
---

# Package Publishing Workflow — 統一的發佈流程觀點

> **核心觀點：** 不管叫什麼名字（Package、Extension、Marketplace），發佈到市場的流程本質上是一樣的。差異只在平台、市場和來源不同。

---

## 概觀

本文件記錄了將套件發佈到市場的標準流程。涵蓋：
- package.json 設定
- GitHub repository 設定
- GitHub Actions CI/CD 自動發佈
- Branch Protection 規則
- NPM Token 與 2FA 處理

**核心概念：** 推送版本 tag（如 `v1.0.0`）→ GitHub Actions 自動執行 `npm publish` → 套件上線。

---

## 統一觀點：所有發佈流程都是一樣的

不管你的套件要發佈到哪裡，核心流程都是一樣的：

```
1. 開發完成
   ↓
2. 版本控制（Git + Tag）
   ↓
3. 自動化發佈（CI/CD）
   ↓
4. 上線到市場
```

### 不同的市場，不同的名字

| 市場 | 平台 | 套件類型 | 檔案格式 | 命令 |
|------|------|----------|----------|------|
| **NPM** | npmjs.com | JavaScript/TypeScript | `.tgz` | `npm publish` |
| **NuGet** | nuget.org | .NET/C# | `.nupkg` | `dotnet nuget push` |
| **PyPI** | pypi.org | Python | `.whl` | `twine upload` |
| **VS Code Marketplace** | marketplace.visualstudio.com | VS Code Extension | `.vsix` | `vsce publish` |
| **GitHub Marketplace** | github.com/marketplace | GitHub Actions/Extensions | `.tar.gz` | GitHub API |

### 相同的概念，不同的實作

| 概念 | NPM | NuGet | PyPI |
|------|-----|-------|------|
| **套件描述** | `package.json` | `.csproj` | `setup.py` / `pyproject.toml` |
| **版本管理** | `npm version` | `dotnet version` | `bump2version` |
| **發布命令** | `npm publish` | `dotnet nuget push` | `twine upload` |
| **Token 管理** | NPM Access Token | NuGet API Key | PyPI API Token |
| **CI/CD** | GitHub Actions | GitHub Actions | GitHub Actions |

### 關鍵洞察

1. **Git 是基礎** — 所有平台都依賴 Git 進行版本控制
2. **Tag 是觸發器** — 大多數平台用 tag 來觸發發佈
3. **CI/CD 是橋樑** — GitHub Actions 可以連接所有平台
4. **Token 是鑰匙** — 每個平台都需要自己的認證 token
5. **流程是統一的** — 只是工具和命令不同

---

## 流程圖

```
1. 開發完成
   ↓
2. 建立 PR（不能直接 push 到 main）
   ↓
3. CI 自動跑 validate
   ↓
4. 合併 PR 到 main
   ↓
5. 打版本 tag（v1.0.0）
   ↓
6. GitHub Actions 自動 npm publish
   ↓
7. 套件上線 NPM
```

---

## 1. package.json 設定

### 必要欄位

```json
{
  "name": "@scope/package-name",
  "version": "1.0.0",
  "type": "module",
  "description": "一句話描述你的套件",
  "author": "你的名字",
  "license": "MIT",
  "repository": {
    "type": "git",
    "url": "git+https://github.com/username/repo.git"
  },
  "bugs": {
    "url": "https://github.com/username/repo/issues"
  },
  "homepage": "https://github.com/username/repo#readme",
  "keywords": ["關鍵字1", "關鍵字2"],
  "engines": {
    "node": ">=18.0.0"
  },
  "publishConfig": {
    "access": "public"
  },
  "files": [
    "dist/",
    "README.md",
    "LICENSE"
  ]
}
```

### 欄位說明

| 欄位 | 說明 | 必要 |
|------|------|------|
| `name` | 套件名稱，scoped package 需加 `@scope/` | ✅ |
| `version` | 語義化版本號 | ✅ |
| `type: "module"` | 使用 ESM 模組 | 建議 |
| `repository` | GitHub repo 連結 | ✅ |
| `bugs` | Issues 頁面 | 建議 |
| `homepage` | 專案首頁 | 建議 |
| `keywords` | 搜尋關鍵字 | 建議 |
| `engines` | Node.js 版本要求 | 建議 |
| `publishConfig.access` | 設為 `"public"`（scoped package 必須） | ✅ |
| `files` | 發佈時包含的檔案 | ✅ |

### Pi Extension 特有欄位

```json
{
  "pi": {
    "extensions": ["./extensions"]
  }
}
```

---

## 2. GitHub Repository 設定

### 2.1 建立 NPM Organization

1. 打開 https://www.npmjs.com/org/create
2. 建立 organization（如 `cheeriocorner`）
3. 選擇 Free 方案
4. 確保你的帳號是 Owner 或 Developer

### 2.2 產生 NPM Access Token

1. 打開 https://www.npmjs.com/settings/tokens
2. 點「Generate New Token」→「Granular Access Token」
3. 設定：
   - **Token name**：`github-actions-publish`
   - **Expiration**：90 天
   - **Automation**：勾選「Allow publishing bypassing 2FA」
   - **Packages and scopes**：選你的 organization → Read and Write
4. 產生並複製 token

### 2.3 設定 GitHub Secret

1. 打開 GitHub repo → Settings → Secrets and variables → Actions
2. 點「New repository secret」
3. Name：`NPM_TOKEN`
4. Secret：貼上你的 NPM token
5. 點「Add secret」

---

## 3. GitHub Actions CI/CD

### 3.1 CI Workflow（PR 驗證）

建立 `.github/workflows/ci.yml`：

```yaml
name: CI

on:
  pull_request:
    branches: [main, master]
  push:
    branches: [main, master]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '22'
          registry-url: 'https://registry.npmjs.org'

      - name: Install dependencies
        run: npm ci --ignore-scripts

      - name: Verify package.json is valid
        run: node -e "require('./package.json'); console.log('✅ package.json is valid')"

      - name: Dry run publish
        run: npm pack --dry-run
```

### 3.2 Publish Workflow（自動發佈）

建立 `.github/workflows/publish.yml`：

```yaml
name: Publish to NPM

on:
  push:
    tags:
      - 'v*'

jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      id-token: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '22'
          registry-url: 'https://registry.npmjs.org'

      - name: Install dependencies
        run: npm ci --ignore-scripts

      - name: Verify version matches tag
        run: |
          PACKAGE_VERSION=$(node -p "require('./package.json').version")
          TAG_VERSION=${GITHUB_REF#refs/tags/v}
          if [ "$PACKAGE_VERSION" != "$TAG_VERSION" ]; then
            echo "❌ Version mismatch!"
            exit 1
          fi
          echo "✅ Version matches: $PACKAGE_VERSION"

      - name: Publish to NPM
        run: npm publish --access public --provenance
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

---

## 4. Branch Protection 規則

在 GitHub repo → Settings → Branches 設定：

| 規則 | 建議設定 |
|------|----------|
| Branch name pattern | `main` 或 `master` |
| Require a pull request before merging | ✅ 開啟 |
| Require status checks to pass | ✅ 開啟，選擇 `validate` |
| Require branches to be up to date | ✅ 開啟 |
| Required approvals | 0（ solo 維護者） |
| Do not allow bypassing | ✅ 開啟 |
| Allow force pushes | ❌ 關閉 |
| Allow deletions | ❌ 關閉 |

**注意：** 如果你是唯一的維護者，將 approvals 設為 0 是合理的，因為只有你有 Write 權限，只有你能 merge PR。

---

## 5. 發佈操作流程

### 新套件首次發佈

```bash
# 1. 確認 package.json 設定正確
npm pack --dry-run

# 2. 推送到 GitHub
git add .
git commit -m "chore: initial release"
git push origin main

# 3. 打 tag 觸發發佈
git tag v1.0.0
git push origin v1.0.0
```

### 後續版本更新

```bash
# 1. 更新版本號
npm version patch  # 1.0.0 → 1.0.1
# 或
npm version minor  # 1.0.0 → 1.1.0
# 或
npm version major  # 1.0.0 → 2.0.0

# 2. 推送變更和 tag
git push origin main
git push origin --tags
```

---

## 6. 常見問題

### Q: 為什麼 CI 報錯404 Not Found？

**A:** 可能原因：
1. NPM Organization 不存在 → 到 npmjs.com 建立
2. 你不在組織成員清單 → 加入組織
3. Token 沒有發佈權限 → 重新產生 token，確保有 Write 權限

### Q: 為什麼報錯 Two-factor authentication required？

**A:** npm 要求發佈時使用 2FA。解決方法：
1. 產生 Granular Access Token
2. 勾選「Allow publishing bypassing 2FA」
3. 用這個 token 設定 GitHub Secret

### Q: 為什麼我的 PR 無法 merge？

**A:** 檢查 Branch Protection 設定：
1. CI 是否通過？→ 等 validate 通過
2. 是否需要 approvals？→ 如果是 solo 維護，設為 0
3. 是否需要 up to date？→ 先 pull 最新版本

### Q: 如何撤銷已發佈的版本？

**A:** 使用 `npm unpublish`（需要 2FA）：
```bash
npm unpublish @scope/package@version --force
```
**注意：** npm 建議只在發佈後 72 小時內撤銷。

---

## 7. 相關資源

- [npm 文檔 - Publishing packages](https://docs.npmjs.com/packages-and-modules/introduction-to-packages#publishing-packages)
- [GitHub Actions - Publishing Node.js packages](https://docs.github.com/en/actions/publishing-packages/publishing-nodejs-packages)
- [npm - Setting up two-factor authentication](https://docs.npmjs.com/configuring-two-factor-authentication)
- [Semantic Versioning](https://semver.org/)

---

## 相關頁面

- [[pi-plannotator-auto]] — 我們的第一個 NPM 發佈實例
- [[pi-web-access-zh-tw]] — 另一個 Pi extension
