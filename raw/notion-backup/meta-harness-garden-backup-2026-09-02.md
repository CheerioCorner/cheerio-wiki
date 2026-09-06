---
Origin: null
Run-ID: ''
Verification-ID: ''
Wiki Link: https://github.com/CheerioCorner/cheerio-wiki/blob/master/wiki/concepts/meta-harness.md
下一步: 持續觀察市場變化；研究大廠策略
子專題: []
專題名稱: Meta-Harness 元鞍具
建立日期:
  end: null
  start: 2026-08-06
  time_zone: null
最後更新:
  end: null
  start: 2026-08-31
  time_zone: null
父專題: []
狀態: 🔬 研究
研究問題: 如何統一管理多個 AI agents？在成本控管、資訊安全、使用者便利、資訊統一之間找到平衡？
🌳 知識花園:
- 3b35979e-3a8c-8199-90ed-cf332d1fc175
- 3b35979e-3a8c-8129-ba33-f558c588a06f
- 3bc5979e-3a8c-81f9-8e54-eea5a2deeeea
🗺️ 視覺地圖:
- 3c55979e-3a8c-81e0-ab8a-cd1a6fb9017b
---

# 🔬 Meta-Harness 元鞍具
> **狀態：** 🔬 研究
> **最後更新：** 2026-08-31
---
## 這是什麼
Meta-Harness（元鞍具）是一個「統一管理多個 AI agents」的研究。
核心問題是：**多個 AI agent 各有強項，但切換和管理帶來巨大挑戰**。
這個研究想要找到一個方法，讓個人和企業都能：
- 用最適合的 agent
- 統一管理成本
- 確保安全
- 保持資訊統一
---
## 為什麼要做這件事
### 問題：多 agent 的挑戰
目前市場上有很多 AI agents，各有強項：
<table header-row="true">
<tr>
<td>Agent</td>
<td>強項</td>
<td>問題</td>
</tr>
<tr>
<td>Claude Code</td>
<td>深度分析</td>
<td>閉源、貴</td>
</tr>
<tr>
<td>Codex</td>
<td>程式碼生成</td>
<td>閉源、貴</td>
</tr>
<tr>
<td>Pi</td>
<td>輕量、可擴展</td>
<td>生態系較小</td>
</tr>
<tr>
<td>GitHub Copilot</td>
<td>整合 IDE</td>
<td>依賴微軟生態</td>
</tr>
<tr>
<td>Cursor</td>
<td>編輯器體驗</td>
<td>閉源、貴</td>
</tr>
</table>
**切換的痛點：**
- 每個 agent 都有自己的設定和工作流
- 切換時需要重新適應
- 知識和 context 不互通
- 成本分散，難以控管
### 挑戰面
<table header-row="true">
<tr>
<td>面向</td>
<td>挑戰</td>
<td>影響</td>
</tr>
<tr>
<td>**成本**</td>
<td>閉源 model token 太貴</td>
<td>個人和企業預算壓力</td>
</tr>
<tr>
<td>**安全**</td>
<td>各家安全策略不統一</td>
<td>風險管控困難</td>
</tr>
<tr>
<td>**便利性**</td>
<td>切換 agent 需要重新設定</td>
<td>工作效率降低</td>
</tr>
<tr>
<td>**資訊統一**</td>
<td>不同 agent 的知識不互通</td>
<td>重複工作、知識流失</td>
</tr>
</table>
### 市場現況
- **閉源模型**：功能強，但成本高
- **開源模型**：成本低，但生態系還在建立
- **大廠策略**：Anthropic、OpenAI、微軟都有自家生態，不容易被統一管理
- **未來不確定**：混合式？開源勝？閉源主導？
### 目標
找到一個「統一管理」的方式：
1. **成本控管** — 集中管理 token 消耗
2. **資訊安全** — 統一安全策略
3. **使用者便利** — 無縫切換 agent
4. **資訊統一** — 知識在 agent 之間流通
---
## 我們怎麼做的
### 第一步：發現 Omnigent
我們發現了 Omnigent——Databricks 開源的 meta-harness。它是目前少數嘗試解決這個問題的專案。
**Omnigent 的解法：**
<table header-row="true">
<tr>
<td>支柱</td>
<td>說明</td>
<td>解決什麼問題</td>
</tr>
<tr>
<td>**Combine**</td>
<td>一行 config 切換 agent</td>
<td>便利性</td>
</tr>
<tr>
<td>**Control**</td>
<td>動態安全策略、成本控制、OS 沙箱</td>
<td>安全、成本</td>
</tr>
<tr>
<td>**Share**</td>
<td>透過 URL 分享 live session</td>
<td>資訊統一</td>
</tr>
</table>
### 第二步：理解限制
但 Omnigent 也有挑戰：
- 目前不支援 Windows（需要 WSL2）
- 大廠不一定會配合（他們想保護自己的生態）
- 社群還很早期
### 第三步：長期研究
這不是一個可以快速解決的問題，而是一個需要長期研究的議題：
- 市場還在變化
- 大廠策略會調整
- 開源生態會成長
- 需要持續觀察和調整
---
## 用了什麼技術/工具/概念
<table header-row="true">
<tr>
<td>技術/工具</td>
<td>用途</td>
<td>怎麼用</td>
</tr>
<tr>
<td>Omnigent</td>
<td>Meta-harness 實作</td>
<td>研究架構</td>
</tr>
<tr>
<td>Runner</td>
<td>統一 API</td>
<td>包裝任何 agent</td>
</tr>
<tr>
<td>OmniBox</td>
<td>OS 沙箱</td>
<td>安全隔離</td>
</tr>
<tr>
<td>Contextual Policies</td>
<td>動態安全策略</td>
<td>追蹤 session 狀態</td>
</tr>
</table>
---
## 目前程度
### 已完成 ✅
- 理解 meta-harness 概念
- 研究 Omnigent 架構
- 建立 Omnigent 視覺地圖
- 比較 Omnigent vs Pi
- 理解市場挑戰和限制
### 進行中 🚧
- 持續觀察市場變化
- 研究大廠策略
### 未開始 ⏳
- 實際部署 Omnigent
- 建立自己的 meta-harness
---
## 成效與感覺
### 感覺
這是一個**很有價值但很難**的研究。
**價值：**
- 個人和企業都有這個需求
- 成本、安全、便利性都是真實痛點
- 市場還在早期，有機會
**挑戰：**
- 大廠會保護自己的生態
- 開源 vs 閉源的未來不確定
- 技術上很難做到「統一管理」
**結論：**
這不是一個可以快速解決的問題，而是一個需要長期追蹤和研究的議題。我們能做的是：持續觀察、理解變化、在適當時機採取行動。
---
## 跟其他專題的關係
<table header-row="true">
<tr>
<td>專題</td>
<td>關聯性</td>
</tr>
<tr>
<td>AI 驅動的開發系統</td>
<td>Meta-harness 是「工具整合」的一環</td>
</tr>
<tr>
<td>AI Agent 架構研究</td>
<td>理解不同 agent 的架構</td>
</tr>
</table>
---
## 還可以做什麼
### 短期
- [ ] 持續觀察 Omnigent 發展
- [ ] 研究其他 meta-harness 方案
### 中期
- [ ] 研究大廠的策略變化
### 長期
- [ ] 建立自己的 meta-harness（如果市場需要）
- [ ] 探索 agent orchestration 的最佳實踐
- [ ] 測試 Omnigent 在 WSL2 的表現（低優先，觀察中，暫不主動測試）
---
## 💡 核心洞察
1. **多 agent 是趨勢** — 沒有一個 agent 能滿足所有需求
2. **切換是痛點** — 個人和企業都面臨切換的挑戰
3. **成本是關鍵** — 閉源太貴，開源在崛起
4. **大廠會保護生態** — 統一管理很難，但值得追求
5. **這是長期研究** — 市場還在變化，需要持續觀察
---
## 🗺️ 專題視覺地圖
[Meta-Harness 元鞍具 視覺地圖](https://app.notion.com/p/3c55979e3a8c81e0ab8acd1a6fb9017b)