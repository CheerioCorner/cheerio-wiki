---
title: YouTube-to-Wiki Pipeline Timing
type: discussion
collection: discussions
topics: [knowledge-mgmt, youtube, pipeline]
status: open
created: 2026-08-02
updated: 2026-08-02
sources: 1
tags: [knowledge-mgmt, youtube, pipeline]
provenance:
  - kind: raw
    path: raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff.md
---

# YouTube-to-Wiki Pipeline Timing

## Current position

Do not build the YouTube pipeline yet. First stabilize the collection model, linking contract, lint rules, and migration policy.

## Working direction

- `youtube-to-wiki` should be an independent skill or workflow.
- It should not be part of `pi-todo-journal`.
- It should not be directly merged into `pi-plannotator-auto`.
- Reusable fetcher or pipeline ideas may be studied later, but raw paths, metadata, local date rules, confirmation flow, and Wiki schema must follow this repository's contracts.

## Gate to revisit

Revisit after shared collections, provenance, canonical linking, lint, and migration phases are stable.

## Related

- [[wiki/decisions/package-knowledge-boundary|Package Knowledge Boundary]]
- [[wiki/concepts/README|Shared collections]]
