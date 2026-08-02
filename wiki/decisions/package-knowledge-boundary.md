---
title: Package Knowledge Boundary
type: decision
collection: decisions
topics: [pi-todo-journal, knowledge-mgmt, okf]
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

# Package Knowledge Boundary

## Decision

`pi-todo-journal` owns session state, explicit tasks, Journal, checkpoints, and user-provided references. It does not own external knowledge ingest, concept canonicalization, Wiki taxonomy, or semantic linking.

## Explicit references

The package may preserve references explicitly supplied by the user, such as:

- URL
- source ID
- Wiki path
- Project Bundle path

These are references, not a semantic knowledge graph or canonicalization engine.

## Independent workflows

`youtube-to-wiki` is an independent skill or workflow. It does not belong inside `pi-todo-journal`, and it is not part of the package's core feature boundary.

## Related

- [[projects/pi-todo-journal/README|pi-todo-journal]]
- [[concepts/okf-open-knowledge-format|OKF]]
- [[decisions/project-okf-bundle-boundary|Project OKF Bundle Boundary]]
