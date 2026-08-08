---
title: learning-loop
type: entity
created: 2026-08-07
updated: 2026-08-07
sources: 1
tags: [skill, learning, automation, meta]
collection: entities
topics: [knowledge-mgmt, ai-agent]
canonical: entities/learning-loop
---

# Learning Loop

> 自動學習優化系統，建立完整的學習迴圈：做 → 記 → 學 → 優化 → 做。

## 一句話

Learning Loop 是一個 skill，讓 Pi 在任務完成後自動提取學到的東西、定期分析工作模式找出瓶頸、追蹤 skill 使用效果並建議改進。

## 核心功能

### 1. 任務後自動沉淀（Post-Mortem Auto-Extract）

任務完成後，自動：
- 提取學習點（決策、問題、解法）
- 更新 wiki 相關頁面
- 更新花園種子
- 寫入 Learning Journal

### 2. 工作模式分析（Pattern Analysis）

定期分析 work history，找出：
- 時間分配比例
- 重複出現的任務類型
- 常見的阻塞點
- 高產出 vs 低產出的活動

### 3. Skill 自我優化（Skill Self-Optimization）

追蹤所有 skill 的：
- 使用頻率
- 成功率
- 最近活躍時間

識別問題並建議：合併、優化、淘汰

## 設計哲學

### 連結比內容重要

學習點要連結到 wiki，不要重複寫一遍。避免資訊散落在多個地方。

### 人類主導優化

Skill 優化建議要人類確認後才執行，避免自動化帶來的風險。

### 不要過度記錄

只記錄有價值的學習，不要為了記錄而記錄。

## 檔案結構

```
work/learning/YYYY-MM.md          # Learning Journal（每月）
work/analytics/YYYY-WXX-pattern.md # 模式分析（每週）
work/skill-health/YYYY-MM.md      # Skill 健康度（每月）
```

## 整合點

- **work-tracker** — 任務完成時自動觸發 post-mortem
- **wiki-knowledge** — 學習點自動寫入 wiki
- **knowledge-garden** — 新 insights 灌溉對應的種子
- **skill-creator** — Skill 優化建議自動傳達

## 相關頁面

- [[wiki/entities/knowledge-garden|knowledge-garden]] — 花園系統
- [[wiki/entities/work-tracker|work-tracker]] — 工作追蹤
- [[wiki/entities/wiki-knowledge|wiki-knowledge]] — Wiki 操作
- [[wiki/entities/skill-creator|skill-creator]] — Skill 建立工具

## 標籤

#skill #learning #automation #meta
