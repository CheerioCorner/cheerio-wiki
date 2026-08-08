---
title: Package Publishing Workflow — 統一的發佈流程觀點
type: concept
created: 2026-07-31
updated: 2026-08-08
sources: 1
tags: [publishing, package, extension, marketplace, ci-cd, npm, nuget, distribution]
collection: concepts
topics: [extension-dev]
canonical: concepts/npm-publishing-workflow
---

# Package Publishing Workflow — 統一的發佈流程觀點

> **核心觀點：** 不管叫什麼名字（Package、Extension、Marketplace），發佈到市場的流程本質上是一樣的。差異只在平台、市場和來源不同。

---

## 統一觀點：所有發佈流程都是一樣的

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

| 市場 | 平台 | 套件類型 | 命令 |
|------|------|----------|------|
| **NPM** | npmjs.com | JavaScript/TypeScript | `npm publish` |
| **NuGet** | nuget.org | .NET | `dotnet nuget push` |
| **PyPI** | pypi.org | Python | `twine upload` |
| **Pi Marketplace** | Pi Agent | Extension | `pi install` |

---

## NPM 發佈完整流程

### 1. package.json 設定

```json
{
  "name": "@your-scope/package-name",
  "version": "1.0.0",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",
  "scripts": {
    "build": "tsc",
    "prepublishOnly": "npm run build"
  },
  "publishConfig": {
    "access": "public"
  }
}
```

### 2. GitHub Actions CI/CD

```yaml
name: Publish
on:
  push:
    tags:
      - 'v*'
jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          registry-url: 'https://registry.npmjs.org'
      - run: npm ci
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

### 3. Branch Protection

- Require pull request reviews
- Require status checks to pass
- Require branches to be up to date

### 4. NPM Token 與 2FA

- 產生 NPM Access Token（Automation type）
- 存入 GitHub Secrets
- 啟用 2FA 保護帳號

---

## 發佈命令

```bash
# 1. 確認所有變更已提交
git status

# 2. 更新版本號
npm version patch  # 1.0.0 → 1.0.1
npm version minor  # 1.0.1 → 1.1.0
npm version major  # 1.1.0 → 2.0.0

# 3. 推送 tag
git push --tags

# 4. GitHub Actions 自動執行 npm publish
```

---

## 實作經驗（pi-plannotator-auto）

### 踩坑紀錄

1. **prepublishOnly 腳本**：確保 build 在 publish 前執行
2. **registry-url**：必須正確設定，否則會 push 到錯誤的 registry
3. **NPM Token 權限**：Automation type 才能繞過 2FA
4. **版本號**：使用 npm version 而不是手動改 package.json

### 成功要素

1. **CI/CD 自動化**：推送 tag 就自動發佈
2. **Branch Protection**：防止直接 push 到 main
3. **2FA 安全**：保護 NPM 帳號
4. **文檔完整**：README + CHANGELOG

---

## 與其他市場的比較

| 面向 | NPM | NuGet | PyPI |
|------|-----|-------|------|
| 語言 | JS/TS | .NET | Python |
| 命令 | npm publish | dotnet nuget push | twine upload |
| Tag 格式 | v* | v* | v* |
| CI/CD | GitHub Actions | GitHub Actions | GitHub Actions |
| 2FA | 支援 | 支援 | 支援 |

**核心洞察：** 流程完全一樣，只是工具和命令不同。

---

## 相關頁面

- [[wiki/concepts/agent-extension-installation|Agent Extension 安裝位置]]
- [[wiki/entities/knowledge-garden|Knowledge Garden]]
