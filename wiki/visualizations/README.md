# Visualizations

> 視覺地圖是 canonical knowledge 的視覺化投影，不是另一個內容 collection。

## Rules

- 所有 wiki 視覺地圖統一存放在本目錄。
- **⚠️ 新增視覺地圖**：直接在 Notion 寫入 Mermaid code block，不需要轉換為 SVG/PNG
- 舊有 Canvas 檔案仍保留，但新內容優先使用 Mermaid
- 每張重要視覺地圖必須由至少一個 Markdown 頁面連入。
- 視覺地圖的文字節點可解釋關係，但不取代 `wiki/concepts/`、`wiki/entities/`、`wiki/sources/` 等 canonical pages。
- Source / topic / architecture map 不再分散到不同 collection；統一由本目錄管理。

## Mermaid 視覺地圖（新格式）

| 地圖 | Topics | 說明 |
|------|--------|------|
| [[wiki/visualizations/harness-seed-map|Harness 種子視覺地圖]] | ai-agent, meta-systems | Harness 概念根內部結構：核心定義、五大職責、三層分類與具體實例 |
| [[wiki/visualizations/obsidian-seed-map|Obsidian 功能全景圖]] | ai-agent, knowledge-mgmt | 核心功能、插件、應用、AI Agent 整合、我們的實作 |

## Canvas 舊格式（保留）

| Canvas | Topics | 說明 |
|--------|--------|------|
| [[wiki/visualizations/pi-agent-control.canvas|PI Agent Control]] | ai-agent | Pi agent 控制流視覺化 |
| [[wiki/visualizations/pi-agent-overview.canvas|PI Agent Overview]] | ai-agent | Pi agent 全景概覽 |
| [[wiki/visualizations/mattpocock-skills.canvas|mattpocock/skills]] | skill | 22 個 skills 視覺化地圖 |
