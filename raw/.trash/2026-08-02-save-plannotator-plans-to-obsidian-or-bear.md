---
title: "Save Plannotator Plans to Obsidian or Bear"
description: "Configure Plannotator to save plans to an Obsidian vault or Bear notes."
source_url: "https://docs.plannotator.ai/open-source/agents/notes"
source_domain: "plannotator.ai"
author:
  - "plannotator.ai"
published:
clipped: 2026-08-02
type: "raw-source"
source_kind: "web"
immutable: "true"
tags:
  - "raw"
  - "web-clip"
---
# Save Plannotator Plans to Obsidian or Bear

> Source: [Save Plannotator Plans to Obsidian or Bear](https://docs.plannotator.ai/open-source/agents/notes)
> Clipped: 2026-08-02

Plannotator can save plans to Obsidian or Bear. Configure the note app from the review UI’s settings.

## Obsidian

1. Open **Settings** in a plan review.
2. Enable Obsidian.
3. Select an auto-detected vault or enter its path.
4. Choose the destination folder. The default is `plannotator`.

Saved Markdown includes YAML frontmatter with creation time, source, and tags. Plannotator derives tags from the plan title and code languages and adds a `[[Plannotator Plans]]` backlink.

## Bear

Enable Bear in **Settings**. Plannotator creates a Bear note with the plan title and body. It adds generated tags or the custom tags you configure.

Bear support is available on macOS because it uses Bear’s local URL scheme.

These note actions save a copy of the plan. The agent’s source plan and Plannotator’s local history remain separate.

Plannotator also supports [Octarine](https://docs.plannotator.ai/open-source/agents/octarine), which requires a workspace name and uses Octarine’s local URL scheme.

Last verified against Plannotator OSS v0.25.0 on July 27, 2026. Maintained by the Plannotator project.

Last modified on July 28, 2026