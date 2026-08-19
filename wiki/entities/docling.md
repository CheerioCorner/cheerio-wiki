---
title: "Docling — IBM 文件解析與結構化框架"
type: entity
created: 2026-08-10
updated: 2026-08-19
sources: 3
tags: [document-understanding, ibm, parsing, ocr, rag, python, lf-ai]
collection: entities
topics: [agent-memory-context]
canonical: entities/docling
---

> Docling 是 IBM Research Zurich 開發的開源文件解析框架，把 PDF 等多種格式的非結構化文件轉換成結構化的 DoclingDocument tree，支援 Chunkless RAG 與 AI 代理整合。現為 LF AI & Data Foundation 專案，GitHub 65k+ stars。

## 基本資訊

| 欄位 | 值 |
|------|-----|
| 開發者 | IBM Research Zurich（AI for knowledge 團隊）→ 現由 LF AI & Data Foundation 托管 |
| GitHub | https://github.com/docling-project/docling |
| Stars / Forks | 65,118 / 4,650 |
| License | MIT |
| 語言 | Python |
| Python 版本 | 3.10+（v2.70.0 後不再支援 3.9） |
| 建立日期 | 2024-07-09 |
| 技術報告 | [arXiv:2408.09869](https://arxiv.org/abs/2408.09869) |
| 文件 | https://docling-project.github.io/docling/ |
| 安裝 | `pip install docling` |
| 平台 | macOS、Linux、Windows（x86_64 和 arm64） |

## 核心功能

### 支援的輸入格式

文件、試算表、簡報、HTML、電子書、音訊、影片、圖片、Email 等：

- **文件類**：PDF、DOCX、ODT、LaTeX、plain text、Markdown supersets（.qmd、.Rmd）
- **簡報類**：PPTX、ODP
- **試算表類**：XLSX、ODS
- **Web 類**：HTML、EPUB
- **圖片類**：PNG、TIFF、JPEG 等
- **音訊類**：WAV、MP3（搭配 ASR 模型）
- **影片類**：MP4、AVI、MOV、MKV、WebM（ASR transcript + 關鍵幀）
- **Email 類**：EML、MSG、Box Notes
- **字幕類**：WebVTT
- **特殊 XML**：DocLang、USPTO 專利、JATS 期刊文章、XBRL 財報

### 輸出格式

Markdown、HTML、WebVTT、DocLang、DocTags、lossless JSON、字典格式等。

### 進階 PDF 理解

- 頁面佈局（page layout）
- 閱讀順序（reading order）
- 表格結構（table structure）
- 程式碼與公式識別
- 圖片分類與理解
- 圖表理解（Bar chart、Pie chart、Line Plot → 轉換為表格或程式碼 + 詳細描述）
- OCR（掃描 PDF 與圖片）

### AI 與代理整合

- **MCP Server**：連接任何 AI 代理
- **API Server**（docling-serve）：把 Docling 當成服務運行
- **VLM 支援**：GraniteDocling（`ibm-granite/granite-docling-258M`）等視覺語言模型
- **CLI**：`docling <url-or-path>` 即可轉換

### 框架整合

LangChain、LlamaIndex、Haystack、Crew AI、Langflow、Metaxy、Semantica 等。

## 架構

Docling 的架構核心是 **Document Converter**，它知道每個格式對應哪個 backend 和 pipeline：

```
輸入文件 → Document Converter → Format Backend + Pipeline → Conversion Result
                                                                    ↓
                                                            DoclingDocument
                                                                    ↓
                                                    ┌───────────────┼───────────────┐
                                                Export          Serializer       Chunker
                                           (Markdown/HTML)    (JSON/XML)     (RAG/Ingestion)
```

關鍵組件：
- **Document Converter**：入口點，管理格式 → backend/pipeline 對應
- **Format-specific Backends**：PDF backend、DOCX backend 等，處理各自的解析邏輯
- **Pipelines**：編排執行步驟（OCR、表格識別、佈局分析等）
- **DoclingDocument**：統一的文件表示格式（Pydantic-based）

## DoclingDocument 格式

Docling v2 引入的統一文件表示，定義在 `docling_core.types.doc`（Pydantic datatype）。

**內容項目（Content Items）：**
- `texts`：所有有文字表示的項目（段落、章節標題、公式等）
- `tables`：所有表格（`TableItem`），可攜帶結構標註
- `pictures`：所有圖片（`PictureItem`），可攜帶結構標註
- `key_value_items`：所有鍵值對項目

**內容結構（Content Structure）：**
- `body`：主文件正文的 tree structure 根節點
- `furniture`：不在正文中的項目根節點（頁首、頁尾等）
- `groups`：容器項目（清單、章節等），不直接代表內容

閱讀順序透過 `body` tree 和每個節點的 children 順序來表達。所有項目透過 JSON pointers 互相參照 parent/child 關係。

## 使用方式

### CLI
```bash
docling https://arxiv.org/pdf/2206.01062
```

### 搭配 VLM
```bash
docling --pipeline vlm --vlm-model granite_docling https://arxiv.org/pdf/2206.01062
```

### Python API
```python
from docling.document_converter import DocumentConverter

source = "https://arxiv.org/pdf/2408.09869"
converter = DocumentConverter()
result = converter.convert(source)
print(result.document.export_to_markdown())
```

## 與 Chunkless RAG 的關係

Docling 是 Chunkless RAG 的基礎設施——它提供乾淨的 tree structure，讓 AI Agent 能透過推理導航文件，而非把文件切成 chunks 做相似度搜尋。詳細說明見 [[wiki/concepts/chunkless-rag|Chunkless RAG]]。

## 與 MarkItDown 的比較

[[wiki/entities/markitdown|MarkItDown]] 也是「萬能格式 → Markdown」轉換器，但 Docling 的差異在於：
- Docling 保留完整的 tree structure（DoclingDocument），不只是轉成純文字
- Docling 針對 PDF 有進階理解能力（reading order、table structure、formula）
- Docling 可直接與 RAG pipeline 整合（Chunkless RAG）
- Docling 支援 MCP Server 和 API Server

## 引用

```bib
@techreport{Docling,
  author = {Deep Search Team},
  month = {8},
  title = {Docling Technical Report},
  url = {https://arxiv.org/abs/2408.09869},
  eprint = {2408.09869},
  doi = {10.48550/arXiv.2408.09869},
  version = {1.0.0},
  year = {2024}
}
```

## 來源

- [[raw/web/2026-08-19-docling-github-readme|Docling GitHub README + Documentation]]
- [[wiki/sources/2026-08-10-chunkless-rag-docling|Chunkless RAG — IBM Technology YouTube]]

## 相關頁面

- [[wiki/concepts/chunkless-rag|Chunkless RAG]] — Docling 之上的 RAG 方法論
- [[wiki/entities/memgraph-rag|MemGraph-RAG]] — 另一種 RAG 進化方向
- [[wiki/entities/markitdown|MarkItDown]] — 另一種格式轉換工具
- [[wiki/concepts/code-knowledge-graph|Code Knowledge Graph]] — 文件結構化的另一個應用場景
