---
title: Neovim LSP configuration 
description: Neovim LSP configuration without tears
draft: true
tags:
    - nvim
    - lsp
---

At some point you might find yourself wanting to stay on the terminal for your development work instead of using something common that works well but eats up memory. If you already use Neovim, you can use an open source Language Server Protocol (LSP) implementation for your languages of choice and turn Neovim into an IDE.

The historic solution up until mid-2023 or so was to use [coc.nvim](https://github.com/neoclide/coc.nvim) and indeed this is what VS Code uses under the hood for their LSP implementation. But what if you cannot or do not want to deal with a NodeJS dependency?

Neovim has a built-in LSP implementation that talks directly to the LSP servers, which makes CoC no longer necessary. This page details how to configure LSP for Neovim, specifically focusing on Golang, C/C++, and Rust. If this is your first rodeo with Neovim you should probably go through [this page](/basic-neovim) first.
