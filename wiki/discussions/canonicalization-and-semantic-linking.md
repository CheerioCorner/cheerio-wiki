---
title: Canonicalization and Semantic Linking
type: discussion
collection: discussions
topics: [knowledge-mgmt, linking, canonicalization]
status: open
created: 2026-08-02
updated: 2026-08-02
sources: 1
tags: [knowledge-mgmt, linking, canonicalization]
provenance:
  - kind: raw
    path: raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff.md
---

# Canonicalization and Semantic Linking

## Working direction

Use an AI-first approach with lint guardrails:

- High-confidence relationships may be created directly.
- Low-confidence canonicalization, parent/child, and contrast relationships should enter an audit for human confirmation.
- A new source should first be checked against the existing index.
- Prefer updating an existing canonical page over creating a synonym or duplicate page.
- Links should express a recoverable knowledge path, not turn every keyword into a link.

## Open questions

- Which relationship sections should become standardized (`Sources`, `Related`, `Builds on`, `Part of`, `Contrasts with`, `Implemented by`, `Used in`)?
- When should a repeated candidate become a canonical page?
- How should compatibility stubs and legacy paths be retired without breaking external references?

## Related

- [[wiki/decisions/shared-wiki-collection-model|Shared Wiki Collection Model]]
- [[wiki/audits/2026-08-02-wiki-collection-topic-link-audit|Collection/topic/link audit]]
