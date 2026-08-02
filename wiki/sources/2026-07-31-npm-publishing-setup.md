---
title: 2026-07-31 Package Publishing 首次實作
type: source
created: 2026-07-31
updated: 2026-07-31
sources: 1
tags: [publishing, package, extension, marketplace, npm, github-actions, ci-cd]
---

# 2026-07-31 Package Publishing 首次實作

> 今天完成了第一個 Package 的發佈流程設定。雖然這次是用 NPM，但學到的流程適用於所有市場。

---

## 背景

- 我們已經開發了 pi-plannotator-auto extension
- 已將原始碼推送到 GitHub：https://github.com/CheerioCorner/pi-plannotator-auto
- 目標是將套件發佈到 NPM，讓其他人可以使用

---

## 完成的工作

### 1. package.json 設定

- 補齊了 repository、bugs、homepage 等欄位
- 設定 `publishConfig.access: "public"`（scoped package 必須）
- 加入 `engines` 指定 Node.js 版本
- 建立 LICENSE 檔案（MIT）

### 2. GitHub Actions CI/CD

- 建立 `.github/workflows/ci.yml` — PR 時自動跑 validate
- 建立 `.github/workflows/publish.yml` — 打 tag 時自動發佈
- 兩個 workflow 都使用 Node.js 22

### 3. Branch Protection

- 設定 Require a pull request before merging
- 設定 Require status checks to pass（validate）
- Required approvals 設為 0（solo 維護者）

### 4. NPM Organization

- 建立了 `cheeriocorner` 組織
- 產生了有「bypass 2FA」權限的 Granular Access Token
- 設定了 GitHub Secret：NPM_TOKEN

---

## 遇到的問題與解決

### 問題 1：npm ci 需要 package-lock.json

**現象：** CI 報錯 `npm ci can only install with an existing package-lock.json`

**解決：** 在本地執行 `npm install --package-lock-only` 產生 lockfile

### 問題 2：Node.js 20 deprecated warning

**現象：** CI 出現 Node.js 20 過時警告

**解決：** 將 workflow 中的 `node-version` 從 `'20'` 改為 `'22'`

### 問題 3：GitHub token 缺少 workflow 權限

**現象：** push 時報錯 `refusing to allow a Personal Access Token to create or update workflow`

**解決：** 更新 GitHub PAT，加入 `workflow` 權限

### 問題 4：npm 2FA 要求

**現象：** `Two-factor authentication or granular access token with bypass 2fa enabled is required`

**解決：** 
1. 產生 Granular Access Token
2. 勾選「Allow publishing bypassing 2FA」
3. 更新 GitHub Secret 和本地 npm 設定

### 問題 5：NPM Organization 404

**現象：** `404 Not Found - PUT https://registry.npmjs.org/@cheerio%2f...`

**解決：** 
1. 建立 NPM Organization（cheeriocorner）
2. 確保帳號是 Owner 或 Developer
3. 更新 package.json 的 name 欄位

---

## 最終結果

- ✅ 套件成功發佈到 NPM：https://www.npmjs.com/package/@cheeriocorner/pi-plannotator-auto
- ✅ GitHub Actions CI/CD 正常運作
- ✅ Branch Protection 設定完成
- ✅ 建立了可重複使用的流程文件

---

## 學到的知識

1. **NPM Scoped Packages** 需要 Organization，且必須設 `publishConfig.access: "public"`
2. **NPM 2FA** 對發佈有嚴格要求，需要使用有 bypass 權限的 token
3. **GitHub Branch Protection** 可以防止直接 push，強制使用 PR 流程
4. **GitHub Actions** 需要 `workflow` 權限才能建立或修改 workflow 檔案
5. **npm ci** 需要 `package-lock.json`，不能用 `npm install`

---

## 下一步

- 將此流程文件化，作為未來 extension 發佈的模板
- 記錄到 Wiki 和 Notion
- 考慮是否需要建立 NuGet 或其他平台的發佈流程

---

## 相關頁面

- [[concepts/npm-publishing-workflow]] — 完整的 NPM 發佈流程文件
- [[projects/pi-plannotator-auto/index]] — 我們的第一個 NPM 發佈實例
