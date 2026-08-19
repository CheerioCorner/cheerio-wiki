# Docling — GitHub README + Documentation

> Source: https://github.com/docling-project/docling
> Documentation: https://docling-project.github.io/docling/
> Technical Report: https://arxiv.org/abs/2408.09869
> Archived: false
> Default branch: main

## What is Docling?

Docling simplifies document processing by parsing diverse formats — including advanced PDF understanding — and providing seamless integrations with the generative AI ecosystem.

## Basic Info

| Field | Value |
|-------|-------|
| Developer | IBM Research Zurich (AI for knowledge team) → now LF AI & Data Foundation project |
| GitHub | https://github.com/docling-project/docling |
| Stars | 65,118 |
| Forks | 4,650 |
| License | MIT |
| Language | Python |
| Python Version | 3.10+ (3.9 support dropped in v2.70.0) |
| Created | 2024-07-09 |
| Topics | ai, convert, document-parser, document-parsing, documents, docx, html, markdown, pdf, pdf-converter, pdf-to-json, pdf-to-text, pptx, tables, xlsx |
| Install | `pip install docling` |
| Platforms | macOS, Linux, Windows (x86_64 and arm64) |

## Features

- Parsing of multiple document formats including PDF, DOCX, PPTX, XLSX, HTML, EPUB, WAV, MP3, WebVTT, Box Notes, email formats (EML, MSG), images (PNG, TIFF, JPEG, ...), LaTeX, DocLang, plain text, and more
- Advanced PDF understanding incl. page layout, reading order, table structure, code, formulas, image classification, and more
- A unified, expressive DoclingDocument representation format
- Various export formats and options, including Markdown, HTML, WebVTT, DocLang, DocTags and lossless JSON
- Support for several application-specific XML schemas including DocLang, USPTO patents, JATS articles, and XBRL financial reports
- Local execution capabilities for sensitive data and air-gapped environments
- Plug-and-play integrations incl. LangChain, LlamaIndex, Crew AI & Haystack for agentic AI
- Extensive OCR support for scanned PDFs and images
- Support for several Visual Language Models, such as GraniteDocling
- Audio support with Automatic Speech Recognition (ASR) models
- Connect to any agent using the MCP server
- Run Docling as a service with the API server (docling-serve)
- Simple and convenient CLI

### What's new (recent additions)

- Parsing of video files (MP4, AVI, MOV, MKV, and WebM) with an ASR transcript and representative keyframes
- Parsing of ODF (OpenDocument Format) files for text documents (.odt), spreadsheets (.ods), and presentations (.odp)
- Parsing of XBRL (eXtensible Business Reporting Language) documents for financial reports
- Parsing of email files (.eml, .msg)
- Parsing of EPUB (Electronic Publication) files for e-books
- Parsing of plain-text files (.txt, .text) and Markdown supersets (.qmd, .Rmd)
- Chart understanding (Barchart, Piechart, LinePlot): convert them into tables or code and add detailed descriptions

### Coming soon

- Metadata extraction, including title, authors, references & language
- Complex chemistry understanding (Molecular structures)

## Architecture

The document converter knows which format-specific backend to employ for parsing and which pipeline to use for orchestrating execution. The conversion result contains the DoclingDocument.

Key components:
- **Document Converter**: Entry point, knows format → backend/pipeline mapping
- **Format-specific Backends**: PDF backend, DOCX backend, etc.
- **Pipelines**: Orchestrate execution steps
- **DoclingDocument**: Unified document representation (Pydantic-based)
- **Export Methods**: Markdown, dictionary, etc.
- **Serializers**: For structured output
- **Chunkers**: For RAG/ingestion use cases

For more details, check the Docling Technical Report (arXiv:2408.09869).

## DoclingDocument Format

Defined as a pydantic datatype in `docling_core.types.doc`:

Content items:
- `texts`: All items with text representation (paragraph, section heading, equation, ...)
- `tables`: All tables (TableItem), can carry structure annotations
- `pictures`: All pictures (PictureItem), can carry structure annotations
- `key_value_items`: All key-value items

Content structure:
- `body`: Root node of tree-structure for main document body
- `furniture`: Root node for items not in body (headers, footers, ...)
- `groups`: Container items (lists, chapters)

Reading order is encapsulated through the `body` tree and children order.

## Integrations

- LangChain
- Langflow
- LlamaIndex
- Metaxy
- Semantica
- Haystack
- Crew AI

## Quickstart

### CLI
```bash
docling https://arxiv.org/pdf/2206.01062
```

### With VLM
```bash
docling --pipeline vlm --vlm-model granite_docling https://arxiv.org/pdf/2206.01062
```

### Python
```python
from docling.document_converter import DocumentConverter

source = "https://arxiv.org/pdf/2408.09869"
converter = DocumentConverter()
result = converter.convert(source)
print(result.document.export_to_markdown())
```

## Citation

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

## LF AI & Data

Docling is hosted as a project in the LF AI & Data Foundation (https://lfaidata.foundation/projects/). The project was started by the AI for knowledge team at IBM Research Zurich.
