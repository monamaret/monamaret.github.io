---
title: Basic Neovim 
description: Configuring and using Neovim 
tags:
    - nvim
---

If you spend a lot of time on the command line you should consider learning how to use one of the modal text editors, like Vi or Vim. Besides the fact that it is objectively cool, Vi is included in the standard set of applications packed into the most basic and stripped-down distros of Linux. If you do a lot of systems administration you'll eventually run into a situation where Vi is your only option, and it is kind of embarassing to panic in front of an audience if you're fixing something in a group setting and don't know how to, say, save and quit the file after making a change.

[Vi and Vim are similar](https://opensource.com/resources/what-vim) but not the same. The nice thing is if you know the basics of using one you can probably figure out the other without too much trouble. Vim is the iMproved version of Vi; Neovim is an even further expanded version of Vim. This page focuses on the basics of configuring and using Neovim. For more complex setup focused on LSP configuration, see [this page](/neovim-lsp).

## Installing Neovim

Neovim is available in most package managers. If you prefer to install from a package or build from source, see [this page](https://github.com/neovim/neovim/wiki/Installing-Neovim) in the official wiki.

## Basic usage

Neovim is launched with the command `nvim` or `nvim [filename]`. If you don't specify a filename on launch it will open to an application information screen that includes some basic commands, like how to open help and how to quit.

The first thing to understand about Neovim is that, as a modal text editor, there are different *modes* for different ways of interacting with the program. When you first open a file you'll be in *Normal Mode* where you can view a file but not edit it. To edit a file, you need to be in *Insert Mode*. To close and save a file, you'll need to be in *Command Mode*.

While in Normal mode, if you type `:help`, you'll access the internal help page which is also available online [here](https://neovim.io/doc/user/#_-general).

You can tell visually whether you're in Normal (can't edit) or Insert (can edit) by the appearance of your cursor: if you have a blinking line, you're in Insert mode and if you have a blinking box, you're in Normal mode.

The most common commands are:

- `i`: switch to Insert mode
- `o`: switch to a new line after the line under the cursor in Insert mode
- `Esc`: switch to Normal mode
- `:`: when in Normal mode, switch to Command mode (you'll see the command pane in the bottom left of the terminal window)
- `:wq`: quit and save the file (write quit)
- `:w`: save the file
- `:q!`: quit without saving even if changes (just `:q` if changes will ask if you know there are unsaved edits)
- `x`: while in Normal mode, deletes the character under the cursor
- `dd`: while in Normal mode, deletes the line under the cursor
- `/`: while in Normal mode, open the search pane

The first habit to get into when using a modal editor is to stay in Normal mode unless you're editing. You can't make any unplanned edits in Normal mode! The second habit to get into is to save when returning to Normal mode. Finally, if you can't figure out what the hell is happening in Neovim, just hit the escape key until you return to Normal mode.

## Modes

Unlike a standard text editor where you can do whatever you want whenever you're in the file, in Neovim you're restricted by what actions you can perform by the current mode.

### Normal mode

Normal mode is where you'll spend the majority of your time in Neovim. To navigate around a file in Normal mode you can use the Vi navigation keys:

- `h`: left
- `j`: down
- `k`: up
- `l`: right

These keys were chosen because they're the home row keys and since you're already on the terminal your hands are already there. If you hate them that's fine, the arrow keys work too.

Most of the Vim power usage with operators (see below) also occurs in Normal mode.

### Insert mode

Insert mode is for editing. You can't type into a document until you're in Insert mode; to enter Insert mode you press `i` from Normal mode. You know you're in Insert mode because it says `-- INSERT --` at the bottom of the window and also because your cursor is a blinking line. Operators don't work in Insert mode, because all the keys are used for normal text entry.

To return to Normal mode from Insert mode after making edits, use the `Esc` key. It's good to habitually save the file every time you switch out of Insert mode by pressing `:w`.

### Visual mode

Visual mode is for selecting and copying text. How complex it is to use will depend on whether you are using a mouse or keyboard only; most terminals will override the Neovim text selection controls and allow you to select and copy text with a mouse in Insert mode but if you're in Normal and select text, you'll switch to Visual. If you're using a keyboard only or want to be very cool then you'll need to read the section below on [configuring the clipboard](#clipboard) for copying as well as the text operators to really understand how to use it effectively. Like `i` for Insert mode, `v` is for Visual, and `esc` will bring you back to Normal. You'll know you're in Visual mode because the cursor is a solid, unblinking block and the mode line says `-- VISUAL --`.

### Command mode

Command mode is when you've entered `:` while in Normal mode. It's the only way to trigger actions like save file (`:w`) or quit without saving (`:q!`). An online version of the command list can be found [here](https://neovim.io/doc/user/vimindex.html).

## Operators and text objects

Neovim has a command concept called *operators* that is used in Normal or Visual mode. Many of these are focused on selecting or moving text, moving the cursor, or otherwise taking some kind of action on a selection of text. These are the source of Neovim's power usage. Operators can be *motion* or *action* verbs.

### Motion operators

Motion operators are used in Normal and Visual mode. There are many, many more than listed below; these are just some of the more common ones. Note that these do **not** include the command `:` before their use as that would change the result entirely:

- `w`: move to the next word
- `b`: move backward to the previous word
- `0`: move to the beginning of the current line
- `^`: move to the first non-blank character on the current line
- `$`: move to the end of the current line
- `[number] Shift + g`: move to the line number at [number]
- `%`: move to the matching bracket (if your cursor is on the closing bracket it will move to the opening)
- `Ctrl + u`: scroll up half a screen
- `Ctrl + d`: scroll down half a screen
- `$`: move to the end of the line
- `G`: move to end of the file

You can also use counters with motion operators; `7w` for example will move the cursor forward seven words. Counters also work with the standard `hkjl` direction motion operators, so `5j` will move the cursor five lines down.

### Action operators

Action operators, or just operators, have to be combined with a motion operator to perform some type of action on the selection. Here are some of the more common ones:

- `d`: delete
- `c`: change
- `y`: copy
- `p`: paste (requires something to have been `y`anked into the register buffer)

Power usage of the operators begins when you start combining motion and action operators on text objects. This is just a tiny selection of what is possible:

- `ciw`: change inside the word below the cursor with the letter typed after
- `dd`: delete the entire line
- `d$`: delete from the cursor to the end of the line
- `y$`: copy from the cursor to the end of the line
- `gu`: make lowercase
- `gU`: make uppercase

### Text objects

Operators act on text objects, which is just a set of characters. Neovim has a [very fine-grained concept of what can be a text object](https://neovim.io/doc/user/vimindex.html#objects). Some of the more commonly used:

- `w`: word
- `aw`: word with white space around it
- `a"`: double-quoted string
- `a'`: single-quoted string

### Undo and redo

Neovim does undo and redo:

- `u`: undo
- `Ctrl + r`: redo

To do the power undo - keep moving back through all the do and undos in your history - you need to configure the undo tree, which is described [later in this document](#undo-tree). When it is configured the command `uu` will work; if it is not configured this command will not do anything.

## Clipboard

Neovim has no direct connection to the system clipboard out of the box. When you use the `y` copy operator, it is copying to and from an internal register which may or may not be a buffer. See [general configuration options](#general-options) for adding support for the system clipboard. See also [this page](https://neovim.io/doc/user/provider.html#clipboard) in the docs for more information.

## Spatial organization

Just because Neovim is a terminal application doesn't mean you're restricted to lines and windows. Neovim supports four layers of abstraction for spatial organization: *buffers*, *windows*, *tabs*, and the *argument list*.

### Buffers

A buffer directly maps to the contents of an open file in memory. Buffers can have three different states:

- `active`: the buffer is displayed in a window
- `hidden`: the buffer is not displayed, but it exists and the file is open
- `inactive`: the buffer is not displayed and it is empty (unlinked to any file)

To see all opened buffers, access the buffer list with the command `:buffers`. Each line in the buffer list contains:

- buffer ID
- buffer status (`%a` for active, `%h` for hidden and `%i` for inactive)
- name of the buffer or filepath relative to current directory if unnamed
- the line number where the cursor is located

To navigate through the buffer list:

- `:buffer [id or name]`: move to the named buffer
- `:bnext` or `:bn`: move to the next buffer in the list
- `:bprevious` or `:bp`: move to the previous buffer in the list

To perform actions on buffers:

- `:bufdo [command]`: applies a command (such as `ZZ`, write if changed and close)
- `:buffers!`: display all buffers including unlisted (will have the indicator `%u`)
- `:badd [filename]`: add a file to the bufferlist
- `:bdelete [ID or name]`: deleted the selected buffer
- `:%bdelete`: delete all buffers

Buffers can be an exceptionally powerful tool if you choose use Neovim as an IDE.

### Windows and tabs

A window is a viewport on a buffer. A tab page is a collection of windows. This might seem like overkill if you're used to a GUI but think about how you actually use an IDE: there are a bunch of files, and you usually have several open at any one time while you move back and forth between them.

Windows are created and closed and organized into tabs, but the buffer is still in the buffer list. See `:help usr_08.txt` for detailed documentation on windows (also available online [here](https://neovim.io/doc/user/usr_08.html)) and `:help tab-page`.

Useful commands:

- `Ctrl + W S` or `:split`: split a window into two viewports on the same file
- `Ctrl +V` or `:vsplit`: split a window but vertically
- `:new`: create a new file in a new window
- `:split_f [filename]`: open a new window containing [filename]
- `:tabnew`: open a new tab
- `:tabclose` or `:tabc`: close the current tab
- `:tabo`: close every other tab but the current one
- `gt`: go to next tab
- `gT`: go to previous tab

Depending on how much screen real estate you have available you may want to take a look directly at the Neovim [documentation page on tabs](https://neovim.io/doc/user/usr_08.html#08.9) before going wild with windows and buffers.

### Argument list

The argument list, or arglist, is best described as a stable subset of the buffer list. Every file in the arglist will be in the buffer list; some files in the buffer list might not be in the arg list. Here are some relevant commands:

- `:args`: display the arglist
- `:argadd`: add a file to the arglist
- `:argdo`: execute a command on all files in the arglist

So how are the arglist and the buffer list different? The buffer list can be modifed by actions unrelated to buffers, like opening a window. The arglist stays the same unless you explicitly modify it. You can access the arglist help with the command `:help arglist`.

## Configuring Neovim

Neovim is configured with a file called `init.vim` that lives in the user's home directory or `~/.config/nvim/init.vim`. The comment delimiter is `"`. Configuration options can be turned on directly within Neovim by using the `:set` command. See `:help options` and `help option-list` for more details, or [this page](https://neovim.io/doc/user/options.html) in the docs.

### Swap file

If you use version control you probably don't need to use a swap file (the swap file prevents you from modifying a file which is open in another Neovim process). Turn it off with this option:

```
" set no swap file
set noswapfile
```

### Undo tree

The built-in undo command will undo and redo the last edits, Neovim has the capability to track every change made to a file and save that to disk. This means you can undo and redo even after closing and reopening a file. Set it like so:

```
" save undo tree in file
set undofile
set undodir=$HOME/.config/nvim/undo

" number of undo saved
set undolevels=10000
set undoreload=10000
```

### General options

Add some nice standard general options:

```
" set line number
set number

" use 2 spaces instead of tab
" copy indent from current line when starting a new line
set autoindent
set expandtab
set tabstop=2
set softtabstop=2
set shiftwidth=2

" set mouse support in Normal and Visual mode
set mouse=nv

" add OS clipboard support
set clipboard+=unnamedplus
```

## Plugins

Neovim is extensible and has a healthy and diverse plugin ecosystem. One thing to keep in mind about plugins is that relying too heavily on those that change standard commands or add a lot of features can make using Vim on different computers difficult or unfamiliar.

### Vim-plug

My preferred plugin manager for Neovim is [vim-plug](https://github.com/junegunn/vim-plug). There are several alternatives; if you don't like vim-plug or want to use a plugin available for another manager like [vundle](https://github.com/VundleVim/Vundle.vim), the nice thing is the install process on all of them is pretty lightweight.

The vim-plug install instructions for Neovim are [here](https://github.com/junegunn/vim-plug#neovim). When you've downloaded vim-plug to your computer you'll also need to add the following to the *beginning* of your `init.vim` file before any other configuration directives:

```
call plug#begin('~/.config/nvim/plugged')
call plug#end()
```

Be sure to change the directory if you're saving your config and plugin files elsewhere. You can see a detailed and commented configuration example [here](https://github.com/junegunn/vim-plug#example) - note the detail in the comments about avoiding plugin directory names like "plugin". Note also that you'll need to use single quotes for paths and plugins in `init.vim` because the double quote is the comment delimiter.

### Installing plugins via vim-plug

Installing plugins is a two part process:

1) Create an entry for the plugin between the `call plug#begin()` and `call plug#end()` statements in your `init.vim` file that looks like so:
    - `Plug 'repositoryowner/repositoryname.vim'`
2) Install the plugin by running the following command in Neovim:
    - `:PlugInstall`

When you run `:PlugInstall` a second window will open in Neovim showing you the progress of the install and, if there were any problems, the error messages.

### Automatically installing the plugin manager

If you [use a script to manage your dotfiles](/dotfiles-management) you can also use it to automatically install vim-plug and your plugins when you set up a new computer. See [this section](https://github.com/junegunn/vim-plug/wiki/tips#automatic-installation) of the vim-plug tips. You can also see a working example in my dotfiles management script [here](https://github.com/monamaret/dotfiles/blob/366587977b36148a567f23b121d2862bed2942ea/install.sh#L11).

### Statusline

Want to configure the statusline in some way? There are a number of plugins that allow you to do all sorts of things to the statusline. I'll admit the primary reason I do this is to enable matching the color scheme of the statusline with the colorscheme of Neovim. Not all colorschemes address the statusline.

I use [Lightline](https://github.com/itchyny/lightline.vim) because it is well-documented. I also like it because it makes it very obvious what mode I am in by changing the color when switching modes.

### Color scheme

Want to change the color scheme of Neovim? There are many plugins available for vim-plug. [This repository](https://github.com/rafi/awesome-vim-colorschemes) has a great list. Add the desired scheme as a plugin in the vim-plug plugin list in your `init.vim`, run `:PlugInstall`, then add the color scheme to your `init.vim` file like so:

```
" color scheme
colo [colorscheme-name]
```

You may need to close and relaunch Neovim for the changes to take effect.

___

### References

- [Opensource.com: What is Vim?](https://opensource.com/resources/what-vim)
- [Neovim Wiki](https://github.com/neovim/neovim/wiki)
- [Neovim user guide](https://neovim.io/doc/user/#_-general)
- [Neovim command index](https://neovim.io/doc/user/vimindex.html)
- [Neovim options](https://neovim.io/doc/user/options.html)
- [Neovim providers: clipboard](https://neovim.io/doc/user/provider.html#clipboard)
- [Neovim user guide part 8: Windows](https://neovim.io/doc/user/usr_08.html)
- [vim-plug tips](https://github.com/junegunn/vim-plug/wiki/tips)
- [awesome-vim-colorschemes](https://github.com/rafi/awesome-vim-colorschemes)