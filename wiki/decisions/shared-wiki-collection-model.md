---
title: Shared Wiki Collection Model
type: decision
collection: decisions
topics: [knowledge-mgmt, wiki-architecture, okf]
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

# Shared Wiki Collection Model

## Decision

The shared Wiki uses five content collections:

- `wiki/concepts/` — reusable abstractions
- `wiki/entities/` — people, tools, packages, services, and concrete implementations
- `wiki/sources/` — processed external material and research notes
- `wiki/decisions/` — confirmed global or cross-project choices
- `wiki/discussions/` — unresolved proposals, research questions, and design discussions

`wiki/topics/` remains as a navigation and taxonomy layer, not the primary content collection.

## Metadata boundary

- `type` describes the document's nature.
- `collection` describes which content collection owns the canonical page.
- `topics` describes subject taxonomy.
- These dimensions are complementary and must not substitute for one another.

## Placement rules

- Project-local decisions stay in `projects/<project-id>/decisions/`.
- Only cross-project or global architecture decisions enter `wiki/decisions/`.
- Unresolved content enters `wiki/discussions/` until confirmed.
- Reusable abstractions enter `wiki/concepts/`.
- Processed external material enters `wiki/sources/`.

## Related

- [[concepts/README|Concepts collection]]
- [[entities/README|Entities collection]]
- [[sources/README|Sources collection]]
- [[decisions/README|Decisions collection]]
- [[discussions/README|Discussions collection]]
- [[topics/README|Topics taxonomy]]
