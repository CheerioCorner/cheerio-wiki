---
title: Project OKF Bundle Boundary
type: decision
collection: decisions
topics: [knowledge-mgmt, okf, project-architecture]
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

# Project OKF Bundle Boundary

## Decision

Obsidian root `projects/<project-id>/` is the Project OKF Bundle location. It stores project knowledge that must survive sessions and local environments, while remaining separate from package source-of-truth systems.

## Boundaries

- Package repository and source code remain the package source of truth.
- Package-local `docs/` is the package's published development and operations bundle.
- Obsidian `projects/` is a cross-session project knowledge bundle.
- The Project Bundle is not a second copy of source code, `PLAN.md`, Todo, release metadata, or package `docs/`.
- Cross-environment canonical references should prefer the project's GitHub repository URL over a fixed local path.
- Project-local decisions remain under `projects/<project-id>/decisions/`.

## Related

- [[concepts/okf-open-knowledge-format|OKF]]
- [[concepts/pi-project-workspace-model|Pi Project / Workspace Model]]
- [[projects/pi-plannotator-auto/index|pi-plannotator-auto Project Bundle]]
- [[raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff|Source handoff]]
