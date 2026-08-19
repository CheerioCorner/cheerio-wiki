---
title: "RAD Methodology for AI Vibe Coding"
type: source
created: 2026-08-18
updated: 2026-08-18
sources: 1
tags: [rad, vibe-coding, agentic-ai, spec-driven-development, software-engineering]
topics: [ai-development-tools, agent-coding-practices]
canonical: sources/2026-08-18-rad-methodology-for-ai-vibe-coding
provenance_raw: "raw/youtube/2026-08-18-rad-methodology-for-ai-vibe-coding.md"
provenance_url: https://www.youtube.com/watch?v=J0zbWsutyA8
---

# RAD Methodology for AI Vibe Coding

> YouTube 影片（10:40），探討 1991 年 James Martin 提出的 RAD 方法論如何對應現代 AI 代理與 vibe coding 開發流程。

## 重點摘要

- **RAD（Rapid Application Development）**：1991 年 James Martin 提出的軟體開發方法論，強調速度、迭代開發與使用者反饋 `[00:14]`
- **核心假設**：使用者在看到東西之前不知道自己要什麼 `[01:40]`
- **四階段模型**：Requirements Planning → User Design → Construction → Cut Over `[01:04]`
- **現代對應**：RAD 的四階段完美映射到 AI vibe coding 開發流程 `[04:13]`
- **關鍵警示**：45% 的 AI 生成程式碼存在安全問題，需要 spec-driven development 驗證 `[07:48]`

## RAD 四階段與 AI 開發流程對應

| RAD 階段 | 傳統定義 | AI 時代對應 |
|----------|---------|------------|
| **Requirements Planning** | 輕量級規劃：定義問題、使用者、功能、限制 `[01:08]` | **Prompt**（提示詞）成為需求文件 `[05:17]` |
| **User Design** | 快速建立可點擊原型，使用者反饋 `[01:59]` | AI 根據 prompt 快速生成可運作原型，使用者反饋後 AI 重新生成 `[05:21]` |
| **Construction** | 實際功能建構，短週期迭代 `[02:35]` | AI 持續產出 data schema、workflow logic、email notifications `[06:20]` |
| **Cut Over** | 部署到 production，資料遷移、使用者培訓 `[02:35]` | 需要 spec-driven development 驗證安全性與邏輯 `[09:00]` |

## 歷史脈絡

- **1982 年**：James Martin 出版《Application Development Without Programmers》`[00:00]`
- **1991 年**：正式形式化 RAD 方法論 `[00:14]`
- **為何沒流行**：當時的 code generators（CASE 工具）功能不夠強，無法建構複雜應用 `[03:39]`
- **復甦原因**：AI agents 讓 prototyping 變得超快，RAD 的四階段方法論因此重新可行 `[03:58]`

## 關鍵論點

### Prototype 是 Keeper，但進 Production 的是 Spec

> "Yes, the prototype is still a keeper. But what actually goes into production is everything the prototype taught us which is captured in that spec." `[09:49]`

- Prototype 是探索需求的學習工具
- 進 production 的是 prototype 教會我們的東西（captured in spec）
- 這就是 spec-driven development 的核心價值

### Self-Approval Loophole 案例

影片用費用審核 App 舉例 `[04:33]`：
- 沒有人的 prompt 說過「員工不能審核自己的費用」`[07:22]`
- AI 很可能沒寫「需要第二人簽核」的規則 `[07:32]`
- 結果：我可以提交 $900 費用然後自己審核通過 `[07:32]`
- 這類問題在 AI 生成程式碼中很常見（45% 有安全問題）`[07:48]`

### RAD 的雙半結構

James Martin 的方法論原本就有兩半 `[08:26]`：
1. **精確描述（Spec）**：系統需要做什麼 `[08:30]`
2. **程式碼生成（Code Gen）**：根據 spec 生成可運作的程式碼 `[08:30]`

現代 AI agents 補上了 code gen 半邊，但 spec 半邊需要由 spec-driven development 來補足 `[09:00]`。

## 來源

- [[raw/youtube/2026-08-18-rad-methodology-for-ai-vibe-coding|Raw Transcript]] — YouTube 逐字稿（manual subtitle）
- 原始影片：[RAD Methodology for AI Vibe Coding](https://www.youtube.com/watch?v=J0zbWsutyA8)

## 相關頁面

- [[wiki/concepts/rapid-application-development]] — RAD 方法論概念頁
- [[wiki/concepts/spec-driven-development]] — Spec-Driven Development 概念頁
- [[wiki/entities/james-martin]] — James Martin 人物頁
- [[wiki/concepts/vibe-coding]] — Vibe Coding 概念頁
- [[wiki/concepts/ai-coding-workflow]] — AI Coding Workflow 概念頁
