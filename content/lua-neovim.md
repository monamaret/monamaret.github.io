---
title: Using Lua within Neovim configuration
description: How to use Lua to configure Neovim
tags:
    - nvim
    - lua
---

You can use Lua script to configure Neovim, but why might you want to do that? The Vim and Neovim API are written in C, and Lua runs in the C engine; since Lua is significantly easier to write and read than C or even Vimscript, Lua can be used to write complex configuration directives.

Another more explicit reason to use it is because many useful Neovim plugins are written in Lua (like [the autocompletion plugins used when setting up Neovim as an IDE](/neovim-lsp)) and require extensive configuration directives that can clutter up your `init.vim` file if they're embedded but can be neatly separated into external scripts called by Lua with a single line, which makes it much, much easier to maintain a lot of plugins.

While you can write your entire Neovim configuration file in Lua, this document will assume that you have an existing `init.vim` file and wish to use some Lua within it. There are two ways to call Lua from your config file: embedding the Lua code directly, and calling an external script.

## Embedded Lua

If you want to keep all you configuration in a single big file, you can embed the Lua portion between a block like so:

```  lua
lua << EOF
  lua print('hi from lua')
EOF
```

You can see a big config example using this in the [nvim-cmp](https://github.com/hrsh7th/nvim-cmp#setup) example setup.

## Calling an external Lua script

If you want to break your configuration into multiple files, you can use the `require` function and call the script from a location within Neovim's `runtimepath`. The standard location is within a `lua` file within the `nvim` folder where your `init.vim` lives:

``` bash
.config
|-- nvim
  |-- autoload/
  |-- init.vim
  |-- lua/
    |-- myluamodule.lua
    |-- othermodules/
      |-- anothermodule.lua
  |-- plugin/
  |-- syntax/
```

Then the following will load `myluamodule.lua` from `init.vim`:

``` lua
require('myluamodule')
```

If the external script is located in a subdirectory it will be called like so:

``` lua
require('othermodules/anothermodule')
-- or
require('othermodules.anothermodule')
```

Note that `require()` not only searching through all `lua/` directories under the `runtimepath`, it will also cache the module on first use. Calling `require()` a second time will _not_ execute the script again and instead return the cached file. To rerun the file, you can remove it from the cache manually first:

``` lua
package.loaded['myluamodule'] = nil
require('myluamodule') -- read and execute the module again from disk
```

To run other Lua scripts automatically on startup, they can go in the `plugin` directory in the `runtimepath`. No `require` function is used in this case.

___

## References

- [Neovim Lua Guide](https://neovim.io/doc/user/lua-guide.html)
- [Lua Reference Manual (Vimdoc version)](https://neovim.io/doc/user/luaref.html)
- [Neovim Lua engine docs](htps://neovim.io/doc/user/lua.html)
- [Opensource.com's Learn Lua Guide](https://opensource.com/article/23/2/learn-lua-guide)
