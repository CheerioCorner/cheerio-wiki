---
title: "ConardLi/garden-skills — 精選 Skill 集合（含 web-design-engineer）"
type: source
created: 2026-08-26
updated: 2026-08-26
sources: 1
provenance_raw: "raw/web/2026-08-26-garden-skills-conardli.md"
tags: [skills, ai-frontend, claude-code, design-collaboration, agent-skill]
topics: [skill-cases-and-comparisons]
upstream: https://github.com/ConardLi/garden-skills
---

# ConardLi/garden-skills

> 5 個子 skill 集合，只有 web-design-engineer 屬前端視覺設計範疇——協作式：宣告設計系統後等待使用者確認才動工。

> ⚠️ **名稱巧合**：此 repo 名稱 `garden-skills` 與本知識庫的 Notion「知識花園」（[[wiki/entities/knowledge-garden|knowledge-garden]]）純屬同名巧合，內容完全無關。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **作者** | ConardLi |
| **Stars** | ~10,700 |
| **Commits** | 142 |
| **授權** | MIT |

## 結構：5 個子 skill

| Skill | 用途 | 前端設計相關？ |
|-------|------|--------------|
| `web-design-engineer` | 建置精緻瀏覽器渲染視覺產物 | ✅ 唯一相關 |
| `beautiful-article` | URL/PDF 轉單檔 HTML 文章 | ❌ |
| `web-video-presentation` | 口播稿轉 16:9 網頁簡報 | ❌ |
| `kb-retriever` | 本機知識庫分層索引檢索 | ❌ |
| `gpt-image-2` | GPT-Image-2 prompt 工程 | ❌ |

## web-design-engineer 核心規則

- Avoid AI clichés（purple→pink gradients、emoji-as-icons、Inter as display font）
- Bold type contrast (4–6× ratio is normal)
- 選 token 前先問四個定位問題：敘事角色/觀看距離/視覺溫度/容量檢查
- **宣告完整設計系統後等待使用者確認才動工**——協作式而非全自動
- 出貨前檢查清單：禁止 scrollIntoView、要求 text-wrap: pretty
- 動效優先序：CSS transition 優先 → React state/RAF → 自訂 timeline

## 來源

- [[raw/web/2026-08-26-garden-skills-conardli|raw: garden-skills GitHub repo 研究報告]]

## 相關頁面

- [[wiki/entities/conardli-web-design-engineer-skill|web-design-engineer Entity]]
- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
