---
title: Existing Topic Stub Cleanup
type: discussion
collection: discussions
topics: [knowledge-mgmt, migration, topics]
status: open
created: 2026-08-02
updated: 2026-08-02
sources: 1
provenance:
  - kind: raw
    path: raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff.md
---

# Existing Topic Stub Cleanup

## Current position

The original `wiki/topics/` pages are retained as compatibility stubs after canonical pages were created in `wiki/concepts/` and `wiki/entities/`.

## Open decision

The final policy for these stubs is not yet fixed:

- retain them permanently as taxonomy-compatible entry points;
- convert them to explicit redirects;
- remove them after all known references are canonicalized.

## Evaluation criteria

- historical links in `wiki/log.md`;
- basename ambiguity between stub and canonical page;
- external or old-session references;
- Obsidian backlink behavior;
- whether the topic directories should remain navigable as taxonomy.

## Related

- [[topics/README|Topics taxonomy]]
- [[decisions/shared-wiki-collection-model|Shared Wiki Collection Model]]
- [[audits/2026-08-02-wiki-collection-topic-link-audit|Collection/topic/link audit]]
