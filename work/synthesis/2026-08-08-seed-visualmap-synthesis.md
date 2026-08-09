# 🔄 綜合分析：前四顆知識花園種子的視覺地圖需求

> **Synthesized from**: Technical, Product, Security, and Maintainability perspectives
> **Date**: 2026-08-08
> **Topic**: 視覺地圖需求 + 內容建議

---

## 📊 四個視角的共識與分歧

### 分析的種子

四個分析涵蓋了 slightly 不同的種子組合：

| 分析 | 種子 1 | 種子 2 | 種子 3 | 種子 4 |
|------|--------|--------|--------|--------|
| **Technical** | Plannotator | NPM Publishing | OKF | mattpocock/skills |
| **Product** | Plannotator | OpenCodeReview | Omnigent | OKF |
| **Security** | OpenCodeReview | Plannotator | NPM Publishing | OKF |
| **Maintainability** | Plannotator | NPM Publishing | OKF | mattpocock/skills |

**觀察**：Plannotator 和 OKF 是所有分析的共同焦點，代表這兩顆種子的複雜度最高。

---

## 🎯 共識區域（四個視角都同意）

### 1. Plannotator — ✅ 已有視覺地圖，需要更新

| 視角 | 結論 | 理由 |
|------|------|------|
| Technical | 更新 | 新增了 Copilot CLI、Codex 等整合 |
| Product | 更新 | 加入與 OCR 的互補關係 |
| Security | 更新 | 加入 trust boundaries（Agent ↔ Browser ↔ Extension） |
| Maintainability | 更新 | 活躍開發中，地圖容易過期但值得維護 |

**綜合建議**：
- **優先度**：🟡 中（已有地圖，不需要從零開始）
- **行動**：更新現有地圖，補上：
  1. 近期新增的 agent 整合（Copilot CLI、Codex）
  2. Security layer（trust boundaries）
  3. 與 OCR 的互補關係
- **地圖類型**：種子地圖（內部結構）+ 部分專題關聯

---

### 2. OKF — ✅ 需要視覺地圖，但時機有分歧

| 視角 | 結論 | 理由 |
|------|------|------|
| Technical | 需要 | 五個核心概念（Knowledge Bundle、Concept Document、Frontmatter、Trust Tier、Provenance）有層級關係 |
| Product | 不建議 | 格式簡單，用表格更合適 |
| Security | 可選 | 風險較低，除非用於 sensitive data |
| Maintainability | 等成熟 | 概念穩定後再畫，畫一次用很久 |

**綜合建議**：
- **優先度**：🟡 中
- **時機**：等到升到 🌿 成長期後再畫（目前是 🌱 種子期）
- **地圖類型**：概念架構圖（不是流程圖）
- **關鍵內容**：
  - Knowledge Bundle → Concept Document → Frontmatter 的包含關係
  - Trust Tier 的三個等級（Unverified → Machine-confirmed → Human-reviewed）
  - 適用場景分支（Extension 文件、AI Agent 知識庫、團隊知識管理）

---

## 🔀 分歧區域（需要權衡）

### 3. NPM Publishing — 觀點分歧最大

| 視角 | 結論 | 理由 |
|------|------|------|
| Technical | 不需要 | 線性流程，用表格已足夠 |
| Product | 不需要 | 流程性知識不是概念網路 |
| Security | **强烈建議** | Supply chain security 需要視覺化攻擊面 |
| Maintainability | 不需要 | 變更頻率低，畫圖增加維護負擔 |

**綜合建議**：
- **優先度**：🟢 低（除非有明確的 supply chain 安全需求）
- **折衷方案**：
  1. **不建立獨立的種子地圖**（三個視角反對）
  2. **在「Extension 開發生態」專題地圖中，作為一個節點出現**（滿足 Security 的部分需求）
  3. **如果要畫，畫 supply chain attack surface**（而非發佈流程）

**理由**：
- NPM Publishing 的核心價值是「流程教學」，不是「概念網路」
- Supply chain security 雖然重要，但可以透過 checklist 而非地圖呈現
- 在專題地圖中顯示它與其他種子的關係，已經足夠

---

### 4. mattpocock/skills — 已有視覺地圖，需要嗎？

| 視角 | 結論 | 理由 |
|------|------|------|
| Technical | 已完整 | 不需要額外製作 |
| Product | — | 未分析此種子 |
| Security | — | 未分析此種子 |
| Maintainability | 等成熟 | 上游依賴，容易過期 |

**綜合建議**：
- **優先度**：🟢 低
- **行動**：保持現狀，可考慮微調：
  - 加入「我們已安裝的 7 個 skills」的 highlight
  - 加入「grill-me → grilling → grill-with-docs」的依賴關係

---

## 🆕 補充分析：Product 視角提到的兩顆種子

### 5. OpenCodeReview (OCR) — 🔴 最優先建立視覺地圖

| 視角 | 結論 | 理由 |
|------|------|------|
| Product | **最優先** | 核心設計哲學「確定性工程 × Agent 混合」需要圖來表達 |
| Security | 需要 | Security architecture 需要明確定義 |

**綜合建議**：
- **優先度**：🔴 高
- **地圖類型**：種子地圖
- **關鍵內容**：
  ```
  OCR 架構圖
  ├── 確定性工程層（強約束）
  │   ├── 精準檔案篩選
  │   ├── 智慧檔案打包
  │   ├── 精細化規則匹配
  │   └── 外掛定位與反思
  ├── Agent 層（動態決策）
  │   ├── 場景化提示詞
  │   └── 場景化工具集
  └── 輸出
      ├── 行級結構化評論
      └── Benchmark 數據
  ```
- **投資報酬率最高**：一張圖能說清楚核心設計哲學

---

### 6. Omnigent — 🟡 建立，但以專題地圖的一部分呈現

| 視角 | 結論 | 理由 |
|------|------|------|
| Product | 可選 | 目前 Windows 不支援，實用性受限 |
| Security | — | 未分析 |

**綜合建議**：
- **優先度**：🟡 中
- **地圖類型**：專題地圖的一部分（Meta-Harness 專題）
- **理由**：Omnigent 的價值在於它是 meta-harness 概念的具體實例，不需要獨立地圖

---

## 📋 綜合優先序建議

| 優先度 | 行動 | 種子 | 預估工時 | 共識度 |
|--------|------|------|----------|--------|
| 🔴 P0 | 建立 **OpenCodeReview 視覺地圖** | OCR | 45 分鐘 | 2/4 視角强烈建議 |
| 🔴 P0 | 補強 **OCR 主觀觀點** | OCR | 15 分鐘 | Product 視角建議 |
| 🟡 P1 | 更新 **Plannotator 視覺地圖** | Plannotator | 20 分鐘 | 4/4 視角同意 |
| 🟡 P1 | 補強 **Plannotator 主觀觀點** | Plannotator | 15 分鐘 | Product 視角建議 |
| 🟡 P2 | 建立 **OKF 視覺地圖**（等成熟） | OKF | 30 分鐘 | 2/4 視角建議，2/4 建議等 |
| 🟢 P3 | 補強 **OKF 主觀觀點** | OKF | 10 分鐘 | Product 視角建議 |
| ⚪ P4 | 在專題地圖中加入 **NPM Publishing 節點** | NPM | 10 分鐘 | Security 視角建議 |
| ⚪ P4 | 微調 **mattpocock/skills 視覺地圖** | skills | 10 分鐘 | Technical 視角建議 |

---

## 🎯 內容缺口分析

根據 Product 視角的分析，四顆種子的**客觀事實層都很完整**（wiki 品質高），但**主觀現實層普遍偏弱**。

### 主觀觀點缺口

| 種子 | 客觀事實 | 主觀觀點 | 需要補強 |
|------|----------|----------|----------|
| Plannotator | ✅ 非常完整 | ⚠️ 有實驗觀察 | 跟 OCR 的互補性觀察 |
| OpenCodeReview | ✅ 非常完整 | ⚠️ 偏客觀描述 | **「我怎麼看它」** |
| Omnigent | ✅ 完整 | ⚠️ 只有平台相容性觀察 | **「meta-harness 跟我們的關係」** |
| OKF | ✅ 非常完整 | ⚠️ 只有「我們的應用」段落 | **「跟 wiki 系統的比較」** |
| NPM Publishing | ✅ 良好 | ⚠️ 缺少交叉引用 | 跟 Plannotator 的發佈經驗 |

**核心洞察**：下一步應該補強的是「我怎麼看這些東西」，而非更多客觀描述。

---

## 🔐 安全考量補充

根據 Security 視角的分析：

| 種子 | 安全風險 | 需要的安全控制 |
|------|----------|----------------|
| OpenCodeReview | 🔴 高 | Code data retention policy、LLM provider security assessment |
| Plannotator | 🟡 中 | Agent permission model、Extension sandboxing |
| NPM Publishing | 🔴 高 | Package signing、2FA、Dependency scanning |
| OKF | 🟡 低 | Schema validation、Content sanitization |

**建議**：在建立視覺地圖時，可以加入 security layer（特別是 Plannotator 的 trust boundaries）。

---

## 🛠️ 製作建議

### 工具選擇
- **Mermaid** → `npx @mermaid-js/mermaid-cli` → SVG → Notion 子頁面
- 或直接在 Notion 中使用 Mermaid code block（如果支援）

### 製作順序
1. **OpenCodeReview**（新頁面，最優先）
2. **Plannotator**（更新現有頁面）
3. **OKF**（等成熟後新頁面）
4. **NPM Publishing**（在專題地圖中加入節點）

### 維護策略
- **Plannotator**：每次新增 agent 整合時更新（高頻率）
- **OKF**：穩定後低頻率更新
- **OCR**：建立後低頻率更新（除非架構大改）
- **NPM Publishing**：在專題地圖中更新即可

---

## 📝 結論

| 關鍵發現 | 說明 |
|----------|------|
| **Plannotator + OKF 是共同焦點** | 四個分析都涵蓋，代表複雜度最高 |
| **NPM Publishing 分歧最大** | Security 視角强烈建議，其他三個反對 → 折衷方案 |
| **OpenCodeReview 最值得投資** | 核心設計哲學需要圖來表達，投資報酬率最高 |
| **主觀觀點是共同缺口** | 所有種子的客觀事實完整，但「我怎麼看它」偏弱 |
| **維護成本 ≠ 畫圖成本** | 關鍵是「過期後要不要重畫」的成本 |

**最終建議**：
1. 先建立 OpenCodeReview 視覺地圖（最優先）
2. 更新 Plannotator 視覺地圖（加入 security layer + OCR 互補）
3. 等 OKF 成熟後建立概念架構圖
4. 補強所有種子的主觀觀點（這是最容易被忽略但最有價值的部分）
