---
title: Neovim LSP configuration 
description: Neovim LSP configuration without tears
draft: true
tags:
    - nvim
    - lsp
---

At some point you might find yourself wanting to stay on the terminal for your development work instead of using something common that works well but eats up memory. If you already use Neovim, you can use an open source Language Server Protocol (LSP) implementation for your languages of choice and turn Neovim into an IDE.

The historic solution up until mid-2023 or so was to use [coc.nvim](https://github.com/neoclide/coc.nvim). But what if you cannot or do not want to deal with a NodeJS dependency?

Neovim has a built-in LSP implementation that talks directly to the LSP server, which makes CoC no longer necessary. This page details how to configure LSP for Neovim, specifically focusing on Bash, C/C++, Go, Kotlin, Rust and SQL. There is also a lot of attention on Lua as several of the completion plugins are configured with Lua.

If this is your first rodeo with Neovim you should probably go through [this page](/basic-neovim) first.

## What is LSP?

I can't do better than the specification overview so read [this](https://microsoft.github.io/language-server-protocol/overviews/lsp/overview/) for an explainer on what LSP actually is.

## Overview of steps requires to implement a new language

Neovim provides an LSP client but servers are provided by third parties. To implement new language support in Neovim you take the following general steps:

1) Install language servers using package manager or the recommended installation instructions. A list of language servers can be found [here](https://microsoft.github.io/language-server-protocol/implementors/servers/).
2) Configure the LSP client per language server.
3) Confirm the server attached to the nvim buffer.
4) Configure keymaps and autocmds to use LSP features.

## Plugins: nvim-lspconfig and autocompletion helpers

While Neovim includes an LSP client, out of the box it makes no assumptions on how to use the client. Things like launching a particular language server, setting initialization options and settings, and attaching new buffers to the currently active language server all require nvim functions, commands and API calls to invoke. Or using a plugin.

Similarly, an IDE-like autocompletion experience is going to need to be configured on a language-specific basis in addition to using some plugins as Neovim's built in *omnifunc* has to be manually triggered to perform autocompletion. How deep you want to get into on autocompletion is definitely a personal preference thing. There are setups out there involving one or no plugins, just extensive configuration file tuning.

This document will use the following plugins:

- [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig): Lspconfig leverages the language client API in Neovim to make using LSP easier. They also provide [server configurations](https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md) for language servers to make setup easier.
- [nvim-cmp](https://github.com/hrsh7th/nvim-cmp/): The base completion plugin. Most of the rest of the completion plugins are related to this one.
- [cmp-nvim-lsp](https://github.com/hrsh7th/cmp-nvim-lsp): Improves on Neovim's built-in omnifunc support (in other words, it breaks omnifunc support); nvim-cmp provides manually-triggered completion to replace omnifunc.
- [cmp-buffer](https://github.com/hrsh7th/cmp-buffer): Provides nvim-cmp with buffer word completion.
- [cmp-path](https://github.com/hrsh7th/cmp-path): Provides nvim-cmp with filesystem path completion.
- [cmp-cmdline](https://github.com/hrsh7th/cmp-cmdline): Provides nvim-cmp with completions within nvim's command line.
- [LuaSnip](https://github.com/L3MON4D3/LuaSnip): Text transformation, conditional expansion, choices, auto-triggered snippets, and customization of the snippets.
- [cmp-luasnip](https://github.com/saadparwaiz1/cmp_luasnip): Enables nvim-cmp to use LuaSnip.
- [friendly-snippets](https://github.com/rafamadriz/friendly-snippets): Collection of community-sources snippets that can be loaded by LuaSnip.

See [this section](/basic-neovim/#plugins) of the Neovim Basics page for more information on installing plugins. Example plugin install configuration for `init.vim`:

``` vim
Plug 'neovim/nvim-lspconfig'
Plug 'hrsh7th/cmp-nvim-lsp'
Plug 'hrsh7th/cmp-buffer'
Plug 'hrsh7th/cmp-path'
Plug 'hrsh7th/cmp-cmdline'
Plug 'hrsh7th/nvim-cmp'
Plug 'L3MON4D3/LuaSnip'
Plug 'saadparwaiz1/cmp_luasnip'
Plug 'rafamadriz/friendly-snippets'
```

## Lspconfig and autocompletion plugin configuration

In your `init.vim`, you will call lspconfig and any desired language servers. You'll want to reference the [server configs](https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md) for each language server you want to use. Because this guide is concerned with implementing the full autocomplete via plugin experience, we will also need to reference the [nvim-cmp LuaSnip](https://github.com/hrsh7th/nvim-cmp/wiki/Example-mappings#luasnip) example mapping.

Lspconfig and language servers will be setup in `init.vim` somewhere after plugins are loaded like so:

``` vim
local lspconfig = require('lspconfig')
lspconfig.lua_ls.setup({})
```

In order to keep our `init.vim` tidy, we will break out the configuration into multiple external Lua scripts and then call them into `init.vim`. See [this page](/lua-neovim) for more information on embedding and importing Lua scripts in Neovim.

### nvim-cmp

WIP

The [default lua language server lspconfig](https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md#lua_ls) assumes you will use Neovim's omnifunc for completion, not autocomplete plugins. [You can't use the omnifunc with nvim-cmp](https://github.com/neovim/nvim-lspconfig/wiki/Autocompletion#nvim-cmp). For this reason the default lua language server for lspconfig will not work for our needs.

## Bash

TBD

## C/C++

TBD

## Go

TBD

## Kotlin

TBD

## Rust

TBD

## SQL

TBD

## Troubleshooting

TBD

___

### References

- [Neovim LSP documentation](https://neovim.io/doc/user/lsp.html)
- [Language Server Protocol](https://microsoft.github.io/language-server-protocol/)
- [nvim-lspconfig README](https://github.com/neovim/nvim-lspconfig#readme)
- [nvim-lspconfig language server configs](https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md)
- [nvim-lspconfig autocompletion wiki page](https://github.com/neovim/nvim-lspconfig/wiki/Autocompletion)
