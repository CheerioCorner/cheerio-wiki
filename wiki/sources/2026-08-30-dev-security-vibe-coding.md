---
title: "Vibe Coding 資安基本功：給非技術人員的資安教學"
type: source
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [security, vibe-coding, CIA, threat-modeling, OWASP]
canonical: sources/2026-08-30-dev-security-vibe-coding
topics: [software-quality]
provenance:
  - kind: raw
    path: raw/youtube/dev-security-vibe-coding.md
---

> 來源：[YouTube — Vibe Coding 資安基本功：給非技術人員的資安教學](https://www.youtube.com/watch?v=t9WA-BkLUps)（22:14，100 segments，繁體中文手動字幕）

## 影片摘要

以 What'Sub 事件為切入點，用白話文介紹 Vibe Coding 時代非技術人員需要具備的資安觀念。核心內容：CIA 原則、上線前五個問題（Threat Modeling）、Codex Security Plugin 使用方式。

## 核心概念

### CIA 原則

資安領域最經典的三大支柱：
- **Confidentiality（機密性）**：確保資料不被無權限的人偷看
- **Integrity（完整性）**：確保資料和規則不被隨意竄改
- **Availability（可用性）**：確保系統隨時都能正常提供服務

### 上線前五個問題（Threat Modeling）

1. **哪些東西被偷會出大事？**（資產盤點 / Assets）
2. **誰能碰哪些資產？**（角色 × 權限配對）
3. **資料路徑哪裡可控、哪裡不可控？**（信任邊界 / Trust Boundary）
4. **哪些底線絕對不能被打破？**（安全不變條件 / Security Invariant）
5. **攻擊面在哪裡？**（Attack Surface）

### 兩道防線

- **Authentication（驗證）**：確認你是誰
- **Authorization（授權）**：確認你能做什麼

### 關鍵設計心法

- **所有保護資產和檢查權限的動作，必須收進信任邊界內的後端來親自把關**
- **後端一律把外部送進來的資料當成可疑包裹**
- **跟錢、點數、權限有關的計算，一律在後端親自重算**
- **永遠要假設外圍防護都已經被突破了，核心資產必須具備自我防衛能力**
- **不要以為使用者會按部就班地操作，必須假設有人用機器人狂按**

### 兩項 High 風險案例

1. **資料庫底層缺少用戶隔離機制**：只在前端做權限控制，繞過前端就能拿到所有資料。解法：Row-Level Security（RLS）
2. **Race Condition（競態條件）**：扣點機制存在時間差，同時送出請求可超額消耗點數。解法：立即預扣機制（如飯店訂房/信用卡）

### Codex Security Plugin 四步流程

1. **Security Scan**：全專案資安體檢
2. **Fix Finding**：AI 自動修補漏洞
3. **Verify Fix**：重新沿攻擊路徑驗證
4. **Security Diff Scan**：每次改版檢查新漏洞

## 陳述級溯源

- 「資安用一句話解釋，就是不管別人怎麼亂搞你的系統，你的軟體功能都能隨時正常運作，而且使用者的資料也都被保護得好好的」`[01:16]`
- 「機密性、完整性、可用性，這三者合起來就是資安領域最經典的 CIA 原則」`[03:38]`
- 「資安的本質其實就是權限控制」`[05:37]`
- 「身分驗證只管你能不能進門，而授權才是管你進門之後，到底有沒有資格碰眼前的資產」`[07:37]`
- 「所有保護資產和檢查權限的動作，儘可能不要交給不可控的前端，必須全部收進信任邊界內的後端來親自把關」`[09:39]`
- 「永遠要假設外圍防護都已經被突破了，你最核心的資產必須具備自我防衛的能力」`[18:16]`
- 「不要以為使用者會按部就班地操作，你必須假設有人會使用機器人，在同一瞬間狂按一萬次」`[19:57]`
- 「功能正常只是起點，把資安守好，做出來的產品才能真正走得長遠」`[22:14]`

## 與 Cheerio 助理的關聯

這支影片的資安觀念直接適用於 Cheerio 助理的設計：
- **CIA 原則**：爬蟲抓到的資料需要機密性保護（不外洩）、完整性（不被竄改）、可用性（系統不中斷）
- **信任邊界**：爬蟲從外部網站抓資料 = 不可控區域，進入 Cheerio 系統 = 可控區域
- **Race Condition**：多個爬蟲 agent 並行時，需要防止重複抓取或重複寫入
- **RLS**：多租戶爬蟲系統需要確保各用戶的爬取任務互不干擾

## 相關頁面

- [[wiki/entities/multi-agent-orchestration-patterns|multi-agent-orchestration-patterns]] — 多 agent 協作的容錯設計
- [[wiki/entities/herdr|herdr]] — 常駐背景 + 狀態偵測的代表工具
