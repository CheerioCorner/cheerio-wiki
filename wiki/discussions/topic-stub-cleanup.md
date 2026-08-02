---
title: Existing Topic Stub Cleanup
type: discussion
collection: discussions
topics: [knowledge-mgmt, migration, topics]
status: resolved
created: 2026-08-02
updated: 2026-08-02
sources: 1
tags: [knowledge-mgmt, migration, topics]
provenance:
  - kind: raw
    path: raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff.md
---

# Existing Topic Stub Cleanup

## Resolution

The former `wiki/topics/<domain>/<page>.md` compatibility stubs have been removed after canonical links were updated. `wiki/topics/` now contains only domain navigation pages and visualizations.

## Policy

- Canonical content belongs in `wiki/concepts/`, `wiki/entities/`, `wiki/sources/`, `wiki/decisions/`, or `wiki/discussions/`.
- Topic directories contain navigation pages only.
- New journal and正文 links use canonical vault-root paths to avoid basename ambiguity.
- Historical `wiki/log.md` links are retained as append-only history and are not repaired by recreating deleted pages.

## Evaluation criteria

- historical links in `wiki/log.md`;
- basename ambiguity between stub and canonical page;
- external or old-session references;
- Obsidian backlink behavior;
- whether the topic directories should remain navigable as taxonomy.

## Related

- [[wiki/topics/README|Topics taxonomy]]
- [[wiki/decisions/shared-wiki-collection-model|Shared Wiki Collection Model]]
- [[wiki/audits/2026-08-02-wiki-collection-topic-link-audit|Collection/topic/link audit]]
