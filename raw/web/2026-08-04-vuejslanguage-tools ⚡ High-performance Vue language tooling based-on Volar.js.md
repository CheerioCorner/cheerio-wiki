---
title: "vuejs/language-tools: ⚡ High-performance Vue language tooling based-on Volar.js"
description: "⚡ High-performance Vue language tooling based-on Volar.js - vuejs/language-tools"
source_url: "https://github.com/vuejs/language-tools"
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
# vuejs/language-tools: ⚡ High-performance Vue language tooling based-on Volar.js

> Source: [vuejs/language-tools: ⚡ High-performance Vue language tooling based-on Volar.js](https://github.com/vuejs/language-tools)
> Clipped: 2026-08-04

## Vue Language Tools

> ⚡ Vue language toolset with native TypeScript performance based-on [Volar.js](https://volarjs.dev/)

## Quick Start

### For VSCode Users

Install the [Vue (Official)](https://marketplace.visualstudio.com/items?itemName=Vue.volar) extension to get full Vue language support.

### Command-Line Type Checking

```
npm install vue-tsc typescript --save-dev
```
```
{
  "scripts": {
    "type-check": "vue-tsc --noEmit"
  }
}
```

## Packages

### For End Users

| Package | Description |
| --- | --- |
| [Vue (Official)](https://github.com/vuejs/language-tools/blob/master/extensions/vscode) | Vue, Vitepress, petite-vue language support extension for VSCode |
| [vue-tsc](https://github.com/vuejs/language-tools/blob/master/packages/tsc) | Type-check and dts build command line tool |

### For Editor Integration

| Package | Description |
| --- | --- |
| [@vue/language-server](https://github.com/vuejs/language-tools/blob/master/packages/language-server) | The language server itself |
| [@vue/language-service](https://github.com/vuejs/language-tools/blob/master/packages/language-service) | Language service plugin collection |
| [@vue/typescript-plugin](https://github.com/vuejs/language-tools/blob/master/packages/typescript-plugin) | TypeScript language service plugin |

### Core Module

| Package | Description |
| --- | --- |
| [@vue/language-core](https://github.com/vuejs/language-tools/blob/master/packages/language-core) | SFC parsing and virtual code generation |

### Helper Tools

| Package | Description |
| --- | --- |
| [vue-component-meta](https://github.com/vuejs/language-tools/blob/master/packages/component-meta) | Component props, events, slots types information extract tool |
| [vue-component-type-helpers](https://github.com/vuejs/language-tools/blob/master/packages/component-type-helpers) | Component type helper utilities |
| [@vue/language-plugin-pug](https://github.com/vuejs/language-tools/blob/master/packages/language-plugin-pug) | Pug template support |

## Community Integration

[yaegassy/coc-volar](https://github.com/yaegassy/coc-volar)  
*Vue language client for coc.nvim*

[neovim/nvim-lspconfig](https://github.com/neovim/nvim-lspconfig)  
*Vue language server configuration for Neovim*, check documentation [here](https://github.com/vuejs/language-tools/wiki/Neovim) to set it up.

[mattn/vim-lsp-settings](https://github.com/mattn/vim-lsp-settings)  
*Vue language server auto configuration for vim-lsp*

[sublimelsp/LSP-volar](https://github.com/sublimelsp/LSP-volar)  
*Vue language client for Sublime*

[kabiaa/atom-ide-volar](https://github.com/kabiaa/atom-ide-volar)  
*Vue language client for Atom*

[emacs-lsp/lsp-mode](https://github.com/emacs-lsp/lsp-mode) ([jadestrong/lsp-volar](https://github.com/jadestrong/lsp-volar))  
*Vue language client for Emacs*

[tommasongr/nova-vue](https://github.com/tommasongr/nova-vue)  
*Vue language client for Nova*

[xiaoxin-sky/lapce-vue](https://github.com/xiaoxin-sky/lapce-vue)  
*Vue language client for Lapce*

[Kingwl/monaco-volar](https://github.com/Kingwl/monaco-volar)  
*Vue language support for Monaco on Browser*

[WebStorm](https://www.jetbrains.com/webstorm/)  
*Built-in integration for `@vue/language-server`*

[Eclipse WildWebDeveloper](https://github.com/eclipse-wildwebdeveloper/wildwebdeveloper)  
*Vue language server configuration for Eclipse*

[![](https://camo.githubusercontent.com/01c84cad9f8f240648e06daa35d85eeb50bbd5719684062cb21e7d848f9d3227/68747470733a2f2f7777772e6d65726d61696463686172742e636f6d2f7261772f39316664303263302d356339312d346637322d613862342d3761663231623763346438363f7468656d653d6c696768742676657273696f6e3d76302e3126666f726d61743d737667)](https://www.mermaidchart.com/raw/91fd02c0-5c91-4f72-a8b4-7af21b7c4d86?theme=light&version=v0.1&format=svg)

## vueCompilerOptions

Configure Vue compiler options in `tsconfig.json`:

```
{
  "compilerOptions": { /* ... */ },
  "vueCompilerOptions": {
    "target": 3.5,
    "strictTemplates": true
  }
}
```

For detailed options, please refer to the [@vue/language-core](https://github.com/vuejs/language-tools/blob/master/packages/language-core) documentation.

## Contribution Guide

If you want to work on the volar extension follow these commands to set up your local development environment.

🔎 Note that you will need pnpm - you can download it here: [https://pnpm.io/installation](https://pnpm.io/installation).

```
git clone https://github.com/vuejs/language-tools.git
cd language-tools
pnpm install
npm run build
```

The recommended way to develop the volar extension is to use the [Debug Tools](https://code.visualstudio.com/Docs/editor/debugging) provided by VSCode.

Alternatively, you can run one of the scripts defined in the [package.json](https://github.com/vuejs/language-tools/blob/master/package.json) file.

❗ You should always use the debug launch configs or package.json scripts defined in the root of the project.

Additional info for contributing to open source projects can be found here: [https://docs.github.com/en/get-started/quickstart/contributing-to-projects](https://docs.github.com/en/get-started/quickstart/contributing-to-projects)

To develop with upstream Volar.js modules, you can setup a workspace with [https://github.com/volarjs/workspace](https://github.com/volarjs/workspace).

## ❤️ Sponsors

This project is made possible thanks to our generous sponsors:

[![](https://camo.githubusercontent.com/566d41e7be3b1d90c540340ef0344cc75c376e81b75fe33577d2665270fe0ee2/68747470733a2f2f63646e2e6a7364656c6976722e6e65742f67682f6a6f686e736f6e636f6465686b2f73706f6e736f72732f73706f6e736f72732e706e67)](https://cdn.jsdelivr.net/gh/johnsoncodehk/sponsors/sponsors.svg)

## License

[MIT](https://github.com/vuejs/language-tools/blob/master/LICENSE) License