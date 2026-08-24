# Deep Research

> 深度研究任務的原始輸出。每個 job 一個資料夾，包含 spec.json、checkpoint、各階段中間產物與最終報告。

## 結構

```
deep-research/
  rc-YYYYMMDD-NNN/            # Job ID（同一天遞增序號）
    spec.json                 # 研究規格（query、sub_questions、深度、預算等）
    checkpoint.json           # 目前進度（research/sources_ready/filtered/rechecked/renamed/answered）
    quality-filter-report.json# 品質過濾結果（保留/移除筆數與理由）
    recheck-log.json          # 每輪補研究的 gap query 記錄（若有觸發）
    rename-manifest.json      # 來源重新命名對照表
    answers.json              # 逐題查詢的原始答案與引用
    research-report.md        # 最終人類可讀報告（含答案、引用、過濾/命名摘要）
```

## 流程

1. `deep-research-intake` skill 釐清意圖 → 產出 `spec.json`
2. `deep-research-execute` skill 執行研究（自動匯入）→ Gemini Notebook 品質過濾 → 不足則補研究（recheck，≤3 輪）→ 分類重新命名 → 逐題查詢 → 產出 `research-report.md`
3. 研究結果經人類確認後，由 `wiki-ingest` 整理進 `wiki/`

## 規則

- Job 資料夾不進 git 版控（執行中間狀態）
- `spec.json` 是唯一的任務定義入口，修改研究範圍請改 spec 而非直接跑
- 研究完成後，精華內容應被 ingest 進 `wiki/`，raw 只保留原始紀錄供追溯
