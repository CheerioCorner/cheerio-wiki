---
title: "Official page for Language Server Protocol"
description: "Language Server Protocol documentation and specification page."
source_url: "https://microsoft.github.io/language-server-protocol/"
source_domain: "github.io"
author:
published:
clipped: 2026-08-04
type: "raw-source"
source_kind: "web"
immutable: "true"
tags:
  - "raw"
  - "web-clip"
---
# Official page for Language Server Protocol

> Source: [Official page for Language Server Protocol](https://microsoft.github.io/language-server-protocol/)
> Clipped: 2026-08-04

The Language Server Protocol (LSP) defines the protocol used between an editor or IDE and a language server that provides language features like auto complete, go to definition, find all references etc. The goal of the Language Server Index Format (LSIF, pronounced like "else if") is to support rich code navigation in development tools or a Web UI without needing a local copy of the source code.

## What is the Language Server Protocol?

Adding features like auto complete, go to definition, or documentation on hover for a programming language takes significant effort. Traditionally this work had to be repeated for each development tool, as each tool provides different APIs for implementing the same feature.

A *Language Server* is meant to provide the language-specific smarts and communicate with development tools over a protocol that enables inter-process communication.

The idea behind the *Language Server Protocol (LSP)* is to standardize the protocol for how such servers and development tools communicate. This way, a single *Language Server* can be re-used in multiple development tools, which in turn can support multiple languages with minimal effort.

LSP is a win for both language providers and tooling vendors!

![VS Code CSS code completion](https://microsoft.github.io/language-server-protocol/img/vscode-css-code-complete.png)

*CSS code complete in VS Code powered by the CSS LSP Server*

![Atom CSS code completion](https://microsoft.github.io/language-server-protocol/img/atom-css-code-complete.png)

*CSS code complete in Atom powered by the CSS LSP Server*

![Eclipse CSS code completion](https://microsoft.github.io/language-server-protocol/img/eclipse-css-code-complete.png)

*CSS code complete in Eclipse IDE powered by the CSS LSP Server*

![VS Code PowerShell hover](https://microsoft.github.io/language-server-protocol/img/vscode-ps-hover.png)

*PowerShell Hover in VS Code powered by the PowerShell LSP Server*

![VS IDE Rust code completion](https://microsoft.github.io/language-server-protocol/img/vs-rust-code-complete.png)

*Rust code complete in Visual Studio powered by the Rust LSP Server*