---
title: "scip-code/scip: SCIP Code Intelligence Protocol"
description: "SCIP Code Intelligence Protocol. Contribute to scip-code/scip development by creating an account on GitHub."
source_url: "https://github.com/scip-code/scip"
source_domain: "github.com"
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
# scip-code/scip: SCIP Code Intelligence Protocol

> Source: [scip-code/scip: SCIP Code Intelligence Protocol](https://github.com/scip-code/scip)
> Clipped: 2026-08-04

## SCIP Code Intelligence Protocol

SCIP (pronunciation: "skip") is a language-agnostic protocol for indexing source code, which can be used to power code navigation functionality such as Go to definition, Find references, and Find implementations.

This repository includes:

- A [Protobuf schema for SCIP](https://github.com/scip-code/scip/blob/main/scip.proto).
- Rich Go and Rust bindings for SCIP: These include many utility functions to help build tooling on top of SCIP.
- Auto-generated bindings for TypeScript and Haskell.
- The [`scip` CLI](https://github.com/scip-code/scip/blob/main/docs/CLI.md), which makes SCIP indexes a breeze to work with.

If you're interested in better understanding the motivation behind SCIP, check out the [announcement blog post](https://about.sourcegraph.com/blog/announcing-scip) and the [design doc](https://github.com/scip-code/scip/blob/main/docs/DESIGN.md).

If you're interested in writing a new indexer that emits SCIP, check out our documentation on [how to write an indexer](https://docs.sourcegraph.com/code_intelligence/explanations/writing_an_indexer). Also, check out the [Debugging section](https://github.com/scip-code/scip/blob/main/docs/Development.md#debugging) in the Development docs.

If you're interested in consuming SCIP data, you can either use one of the [provided language bindings](https://github.com/scip-code/scip/tree/main/bindings), or generate code for the [SCIP Protobuf schema](https://github.com/scip-code/scip/blob/main/scip.proto) using the Protobuf toolchain for your language ecosystem. Also, check out the [Debugging section](https://github.com/scip-code/scip/blob/main/docs/Development.md#debugging) in the Development docs.

## Tools using SCIP

Several indexers currently emit SCIP data:

- [scip-java](https://github.com/sourcegraph/scip-java): Java, Scala, Kotlin
- [scip-typescript](https://github.com/sourcegraph/scip-typescript): TypeScript, JavaScript
- [rust-analyzer](https://github.com/rust-lang/rust-analyzer): Rust
- [scip-clang](https://github.com/sourcegraph/scip-clang): C++, C
- [scip-ruby](https://github.com/sourcegraph/scip-ruby): Ruby
- [scip-python](https://github.com/sourcegraph/scip-python): Python
- [scip-dotnet](https://github.com/sourcegraph/scip-dotnet): C#, Visual Basic
- [scip-dart](https://github.com/Workiva/scip-dart): Dart
- [scip-php](https://github.com/davidrjenni/scip-php): PHP
- [debian-lsp](https://github.com/jelmer/debian-lsp): Debian packaging files

For more details about indexers, see the [Sourcegraph documentation](https://docs.sourcegraph.com/code_navigation/references/indexers).

Other tools which use SCIP include the [Sourcegraph CLI](https://github.com/sourcegraph/src-cli), and the SCIP CLI in this repo.

## Installing the scip CLI

You can find binaries for the `scip` CLI tool [here](https://github.com/scip-code/scip/releases). You can also compile a binary locally using:

```
git clone https://github.com/scip-code/scip.git --depth=1
cd scip
go build ./cmd/scip
```

You can consult the [CLI reference](https://github.com/scip-code/scip/blob/main/docs/CLI.md) or `--help` for usage information.

## Contributing

We welcome questions, suggestions as well as code and docs contributions.

For submitting contributions, check out [Development.md](https://github.com/scip-code/scip/blob/main/docs/Development.md) to better understand project structure and common workflows.

Contributors should abide by the [Code of Conduct](https://scip-code.org/code-of-conduct.html).