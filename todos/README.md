# Todos 系統

> 所有任務的唯一真相來源。Pi Agent 啟動時會讀取這裡。

## 架構

```
todos/
├── README.md           ← 本文件（格式規範）
├── current.md          ← 目前進行中的任務（每次只專注 1-3 個）
├── backlog.md          ← 待辦清單（按優先級排列）
├── done/               ← 已完成（按日期歸檔）
│   └── YYYY-MM-DD.md
└── archive/            ← 舊的已完成項目（超過 30 天）
```

## 格式規範

### 任務格式

```markdown
- [ ] 任務名稱 ⏫|🔼|🔽| #tag 📅 YYYY-MM-DD
```

**優先級：**
- `⏫` — 高（本週內完成）
- `🔼` — 中（本月內完成）
- `🔽` — 低（有空再做）
- 無標記 — 一般

**標籤（#tag）：**
- `#meta` — 系統維護
- `#wiki` — 知識庫
- `#notion` — Notion 整合
- `#skills` — Skill 開發
- `#pi-web` — Pi Web/Desktop 專案
- `#extension` — Extension 開發
- `#knowledge` — 知識管理
- `#exploration` — 探索研究

### 已完成格式

```markdown
- [x] 任務名稱 #tag 📅 YYYY-MM-DD
```

## 操作流程

### 新增任務
1. 在 `backlog.md` 對應優先級區塊新增
2. 如果是本週要做的，放到 `current.md`

### 完成任務
1. 從 `current.md` 或 `backlog.md` 移除
2. 在 `done/YYYY-MM-DD.md` 新增一條紀錄
3. 如果 `done/` 檔案超過 30 天，移到 `archive/`

### 每日檢查
1. 讀 `current.md` 確認今天要做什麼
2. 完成後更新狀態
3. 寫日記時同步更新

## 與其他系統的整合

- **Pi Agent 啟動** — 讀取 `C:/Cheerio/Obsidian/todos/current.md`
- **日記** — 在 `journal/daily/YYYY-MM-DD.md` 中引用今日任務
- **Wiki lint** — 檢查是否有遺忘的任務
