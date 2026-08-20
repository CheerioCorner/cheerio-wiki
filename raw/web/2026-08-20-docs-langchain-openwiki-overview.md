---
title: "OpenWiki - Docs by LangChain"
type: raw-web
created: 2026-08-20
url: "https://docs.langchain.com/oss/openwiki/overview"
---

# OpenWiki - Docs by LangChain

OpenWiki - Docs by LangChain                                              Documentation Index  Fetch the complete documentation index at:   /llms.txt   Use this file to discover all available pages before exploring further.         Skip to main content                 OpenWiki is an open source CLI that writes and maintains a Markdown wiki about your codebase or personal knowledge. The wiki captures details such as architecture, integrations, evals, and workflows so  coding agents  can use it as durable context instead of rediscovering the repository on every task.

That makes agent work faster and cheaper in tokens: agents read a curated wiki first, then inspect source only where they need more detail. Humans can browse the same Markdown (and the local  visualizer ), but the primary audience is agents.

OpenWiki is built on  Deep Agents  and supports tracing with  LangSmith .

​     Get started

Install the CLI, then initialize documentation for the current repository:

npm   install   -g   openwiki

openwiki   --init

See the  Quickstart  to choose a model provider, generate docs, and keep them up to date.

OpenWiki does not provide a formal connector for Claude or Codex. In code mode, it adds pointers to the generated wiki in the repository-root  AGENTS.md  and  CLAUDE.md  files, so compatible coding agents can discover and consult the wiki.

​     Modes

OpenWiki has two modes:

Mode  Command  Output  Use when       Code  (default)   openwiki  /  openwiki code    openwiki/  in the current repository  You want repository context and documentation for coding agents     Personal    openwiki personal    ~/.openwiki/wiki   You want a local personal brain from configured sources

Bare  openwiki --init  and  openwiki --update  run in code mode. Use  openwiki personal --init  or  openwiki personal --update  for the personal wiki.

​     Capabilities

Repository wikis   Generate Markdown docs under  openwiki/ , then wire them into  AGENTS.md  and  CLAUDE.md  so coding agents can find them.             Personal brain   Build a local wiki from git repos, Gmail, Notion, web search, Hacker News, and X/Twitter.             Automatic updates   Refresh docs from GitHub Actions, GitLab CI, or Bitbucket Pipelines and open a PR when content changes.             Model providers   Use OpenAI, Anthropic, Gemini, Bedrock, OpenRouter, GitHub Copilot, and other providers out of the box.             Open Knowledge Format   Emit OKF v0.1 Markdown bundles with front matter, indexes, and linked concepts.             LangSmith tracing   Trace documentation runs with LangSmith.

​     Next steps

Quickstart   Install OpenWiki, configure a provider, and generate your first wiki.             CLI reference   Review commands, flags, and connector subcommands.

Connect these docs  to Claude, VSCode, and more via MCP for real-time answers.         Edit this page on GitHub  or  file an issue .        Was this page helpful?     Yes    No                ⌘ I
