---
title: "Google Cloud 團隊提出用 AI 現代化 legacy code 的方法論"
type: conversation
created: 2026-08-25
related_raw: raw/youtube/how-to-modernize-legacy-codebases-ai-coding-agents
source_kind: conversation
---

# Cheer 的想法：Google Cloud Legacy Code × AI 方法論

> 來源：https://youtu.be/RKvckuC6Bsk

## 核心觀察

這支影片主要是由 Google Cloud 團隊所提出來的，我會看這個，主要是因為這是每一間企業都會面臨到的問題：有太多舊的程式，甚至沒有完整的手冊，只有程式碼，而且這些程式碼也沒有結構化過；另外它甚至沒有自動化測試，也沒有 CI/CD。那我們要如何用什麼樣的方法論，讓 AI 可以去優化或建置我們的系統？

## 三個關鍵概念

在這裡我覺得很有趣的是，Google 團隊提出了幾個概念：

### 1. Sandbox（沙盒）

他們提出來的是用 Docker。Docker Compose 也是我們這邊一直想再深度研究的方向，但因為 Docker Desktop 基本上是要收費的，所以我們有在研究是否有其他方案可以取代 Docker Desktop 的部分。

### 2. 充分討論

在整個 AI 開始 coding 之前，人要充分跟 AI 討論過，這其實就是 Prompt Engineering 的一部分。

### 3. TDD 與 BDD

他們提出應該要先用 TDD 來找出所有可能受影響的系統層面；在驗證 AI 品質的部分，則應該使用 BDD 來完成系統品質的保證。其實也就是現在常說的，用更多的測試程式來保證系統的穩定度，即使這套東西是 AI 寫出來的。

## 與自身工作的關聯

目前為止，這其實跟我們的想法蠻契合的，只是我們之前並沒有非常具體化的步驟出來。
