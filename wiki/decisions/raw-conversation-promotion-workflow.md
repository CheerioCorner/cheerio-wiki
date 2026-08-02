---
title: Raw Conversation Promotion Workflow
type: decision
collection: decisions
topics: [knowledge-mgmt, provenance, annotator]
status: confirmed
created: 2026-08-02
updated: 2026-08-02
sources: 1
provenance:
  - kind: raw
    path: raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff.md
verified:
  by: human:Cheer
  at: 2026-08-02
---

# Raw Conversation Promotion Workflow

## Decision

Annotator feedback and session handoffs are captured as immutable raw conversations first. They are later promoted into canonical Wiki pages according to document nature and confirmation state.

## Workflow

1. Save the original conversation under `raw/conversations/`.
2. Use `type: raw-conversation` and `immutable: true`.
3. Keep unresolved proposals in `wiki/discussions/`.
4. Promote confirmed global or cross-project architecture and technology choices to `wiki/decisions/`.
5. Promote reusable abstractions to `wiki/concepts/`.
6. Promote processed external material to `wiki/sources/`.
7. Add `provenance` to every promoted page so the canonical page can be traced back to the raw conversation or external source.

## Integrity rules

- Raw conversation content is not rewritten to match the promoted page.
- Promotion is a new interpretation or canonicalization layer, not a replacement of the source record.
- `verified.by` uses `human:Cheer` for human verification.

## Related

- [[concepts/README|Concepts]]
- [[decisions/README|Decisions]]
- [[discussions/README|Discussions]]
- [[raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff|Source handoff]]
