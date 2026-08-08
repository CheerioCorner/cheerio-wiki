---
title: MarkItDown
type: entity
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [microsoft, markdown, converter, pdf, document-processing, python, llm-friendly]
topics: [extension-dev, knowledge-mgmt]
canonical: entities/markitdown
---

# MarkItDown

> Microsoft 出品的「萬能格式 → Markdown」轉換器，專為 LLM 和文字分析流程設計。

## 一句話

MarkItDown 把 PDF、Word、Excel、PPT、圖片、音訊、HTML 等各種格式轉成 Markdown，讓 LLM 能直接讀懂。

## 基本資訊

| 項目 | 內容 |
|------|------|
| 作者 | Microsoft AutoGen Team |
| GitHub | https://github.com/microsoft/markitdown |
| Stars | 172,253+ |
| 授權 | MIT |
| 語言 | Python（需 3.10+） |
| 安裝 | `pip install 'markitdown[all]'` |

## 支援格式

| 格式 | 說明 |
|------|------|
| PDF | 完整支援，保留標題、列表、表格結構 |
| PowerPoint (.pptx) | 投影片文字 + 結構 |
| Word (.docx) | 完整文件結構 |
| Excel (.xlsx/.xls) | 表格資料 |
| 圖片 | EXIF metadata + OCR（可選 LLM 描述） |
| 音訊 | EXIF metadata + 語音轉文字 |
| HTML | 網頁內容 |
| CSV / JSON / XML | 結構化文字 |
| ZIP | 迭代內容 |
| YouTube URL | 影片字幕 |
| EPub | 電子書 |

## 為什麼選 MarkItDown？

### vs pdftotext

| 面向 | pdftotext | MarkItDown |
|------|-----------|------------|
| 輸出格式 | 純文字 | 結構化 Markdown |
| 表格保留 | ❌ 壓成一行 | ✅ Markdown 表格 |
| 標題結構 | ❌ 需手動辨識 | ✅ `# / ## / ###` |
| 列表保留 | ⚠️ 不穩定 | ✅ `- / 1.` |
| 圖片處理 | ❌ | ✅ OCR / LLM 描述 |
| 安裝 | 需 poppler 套件 | pip install |
| 跨格式 | 僅 PDF | PDF + Word + Excel + PPT + ... |

### vs PyMuPDF (fitz)

| 面向 | PyMuPDF | MarkItDown |
|------|---------|------------|
| 定位 | PDF 操作工具庫 | 格式轉換器 |
| Markdown 輸出 | ❌ 需手動格式化 | ✅ 原生支援 |
| 圖片提取 | ✅ 專長 | ⚠️ 基礎 |
| 文字提取 | ✅ | ✅ |
| 跨格式 | 僅 PDF | PDF + Word + Excel + PPT + ... |

### vs 手動整理

MarkItDown 自動保留文件結構，不需要人工辨識標題、列表、表格。

## 使用方式

### CLI

```bash
# 基本用法
markitdown path-to-file.pdf > document.md

# 指定輸出檔案
markitdown path-to-file.pdf -o document.md

# 管道
cat path-to-file.pdf | markitdown
```

### Python API

```python
from markitdown import MarkItDown

md = MarkItDown()
result = md.convert("document.pdf")
print(result.text_content)
```

### LLM 增強（圖片描述）

```python
from markitdown import MarkItDown
from openai import OpenAI

md = MarkItDown(
    llm_client=OpenAI(),
    llm_model="gpt-4o"
)
result = md.convert("document_with_images.pdf")
```

## 可選依賴

```bash
# 全部安裝
pip install 'markitdown[all]'

# 只装特定格式
pip install 'markitdown[pdf, docx, pptx]'
```

| 參數 | 格式 |
|------|------|
| `[pdf]` | PDF |
| `[docx]` | Word |
| `[pptx]` | PowerPoint |
| `[xlsx]` | Excel |
| `[xls]` | 舊版 Excel |
| `[outlook]` | Outlook 郵件 |
| `[audio-transcription]` | 音訊轉文字 |
| `[youtube-transcription]` | YouTube 字幕 |
| `[az-doc-intel]` | Azure Document Intelligence |
| `[az-content-understanding]` | Azure Content Understanding |

## 插件系統

```bash
# 列出已安裝插件
markitdown --list-plugins

# 使用插件
markitdown --use-plugins path-to-file.pdf
```

### markitdown-ocr 插件

用 LLM Vision 做 OCR，從 PDF/Word/PPTX 的圖片中提取文字：

```bash
pip install markitdown-ocr openai
```

```python
from markitdown import MarkItDown
from openai import OpenAI

md = MarkItDown(
    enable_plugins=True,
    llm_client=OpenAI(),
    llm_model="gpt-4o"
)
result = md.convert("scanned_document.pdf")
```

## 與我們知識庫的整合

MarkItDown 可以直接整合進 ingest 流程：

```bash
# 任何格式 → Markdown → 進 raw/ → ingest 到 wiki
markitdown any-file.pdf -o raw/web/2026-08-08-filename.md
```

這比 `pdftotext` + 手動整理更有效率。

## 安全注意事項

- MarkItDown 以當前進程權限執行 I/O
- 不要直接處理不可信輸入
- 优先使用 `convert_local()` 處理本機檔案
- 限制 URI scheme 和網路目標

## 來源

- GitHub：https://github.com/microsoft/markitdown
- PyPI：https://pypi.org/project/markitdown/
- AutoGen Team 出品

## 相關頁面

- [[wiki/entities/plannotator|Plannotator]] — 另一種文件處理工具（專注 review）
- [[wiki/topics/knowledge-mgmt|知識管理]] — 我們的 ingest 流程
