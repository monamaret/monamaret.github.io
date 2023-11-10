---
title: Configuring tmux 
description: Basic Tmux configuration 
tags:
    - tmux
---

If you want to become a Tmux power user the gold standard reference is of course [the wiki](1) but it can be a bit overwhelming if you're just getting started and are trying to figure out the basics. There are also many good [beginner guides](https://www.redhat.com/sysadmin/introduction-tmux-linux) that go deep on why you might want to learn how to use Tmux and what it does for you. This page, however, is going to focus only on how to configure Tmux and some basic helpful tips, like how to copy/paste and how to scroll back through your history.

This guide assumes you've already installed Tmux. If you haven't done that yet, it is available in most package manager repositories as `tmux`. If you want the detailed instructions, see the wiki page on installing [here](https://github.com/tmux/tmux/wiki/Installing).

## Some basic commands to get started

Tmux is mostly used with a keyboard and has an extensive set of keyboard commands. This can be intimidating the first time you launch it and aren't sure how it is any different from a regular terminal interface aside from the status line. You launch tmux from a terminal by typing `tmux new-session`. Within tmux, all commands are prefaced by a leader or command binding. The default is `Control + b`. After you enter this you have a couple of seconds to enter a second key or keys to take a specific actions. Here are some of the most common, note that the commands are case-sensitive:

- `Ctrl+b d`: Detach from the current session. This does not end the Tmux session, so you can start a long-running command and then detach and it will continue without your interaction. Type `tmux attach` outside of tmux to re-attach to the running session.
- `Ctrl+b %`: Create a new pane to the right of the current pane.
- `Ctrl+b "`: Create a new pane below the current pane.
- `Ctrl+b [arrow]`: Navigate to another pane in the direction of the arrow pressed.
- `Ctrl+b RAlt [arrow]`: Resize a pane. Hold the Right Alt key while pressing the arrow in the direction to resize.
- `Ctrl+b x`: Kill the current pane.
- `Ctrl+b c`: Create a new window.
- `Ctrl+b &`: Kill current window.
- `Ctrl+b n`: Move to the next window (the window index is listed in the status line by default and the window name will be the running application).
- `Ctrl+b w`: List all windows.
- `Ctrl+b :`: access the tmux command prompt
- `tmux kill-server`: kill all tmux sessions. This is run from outside tmux.
- `tmux new -s [NAME]`: Start a new tmux session named [NAME].
- `tmux attach -t [NAME]`: Attach to the named session.

It's also helpful to understand going in that tmux has three discrete levels of organization: sessions, windows and panes. See [this section](https://github.com/tmux/tmux/wiki/Getting-Started#sessions-windows-and-panes) of the wiki getting started guide to understand the difference.

## The config file

Tmux is configured by editing a file named `tmux.conf` that will be placed in your home directory after installing or in `~/.config`.

Comments in the config file are prefaced by `#` and are ignored. Configuration directives are tmux commands are are executed in the order they appear in the file. Environment variables can be set in the tmux file but shell constructs like `$()` will not be read.

Note that the default `tmux.conf` will be created as an invisible file in the user's home directory (`~/.tmux.conf`), but if you move it to the config directory, it needs to be visible (`~/.config/tmux/tmux.conf`) in order to be sourced by tmux.

## Reloading the config file after changes

If you make changes to the config file, you can reload the changed file from a running tmux server by accessing the command prompt (`Ctrl+b :`) and typing the command `source` with the location of the config file:

```
:source ~/.config/tmux.conf
```

The status line will temporarily change color to indicate the config has been reloaded.

You can also set a new keybinding to make sourcing the config file easier by adding the following to the config file (make sure you enter the correct location if your file is in a different location than below):

```
unbind r
bind r source-file ~/.config/tmux/tmux.conf \; display "Reloaded tmux conf"
```

After setting this, before using it the first time, you'll need to kill the tmux server and then relaunch in order to pick up the changes. After that you can use the new keyboard command (`Ctrl+b r`) to reload the config without restarting the server.

## History buffer and scrolling back

Without additional configuration you can't scroll back through your session history. Add this functionality with the following:

```
set -g history-limit 10000
```

## Copy and paste

Like [Neovim](/basic-neovim), tmux has modes for general use and a separate mode for copying. Also like Neovim, it does not have access to the system clipboard and when you use copy mode you are copying into a tmux-only register (the tmux paste buffer). The topic of copy and paste within tmux can get the security people excited; it's helpful to read the [wiki page on the clipboard](https://github.com/tmux/tmux/wiki/Clipboard) at least once so you understand some of the risks in allowing external applications to access the tmux paste buffer.

To copy out of tmux, you'll need to switch into copy mode and also have an external clipboard configured. If you plan on using a mouse, you can skip switching into copy mode.

How to to copy and paste within tmux with just the keyboard and copy mode:

1) To enter copy mode, use the leader command and left bracket (`Ctrl + b [`). You will know you're in copy mode because there will be two numbers in yellow in the upper right corner of the window.
2) Use the arrows to get to the position you want to start copying from, and then press `Ctrl + Space`. Use the arrow keys to move to the end of the text you want to copy.
3) When you have all the text selected that you want to copy, press `Ctrl + w` to copy into the tmux register.
4) Press `Ctrl + b ]` to paste into a different tmux window.

To configure tmux to use the system clipboard you'll need to add the `set-clipboard` directive to your `tmux.conf` file. The `set-clipboard` directive takes one of three arguments:

- `set -s set-clipboard on`: tmux and external applications can set the clipboard for the tmux paste buffer
- `set -s set-clipboard external`: only tmux can set the clipboard for the tmux paste buffer
- `set -s set-clipboard off`: no clipboard is set

Setting a clipboard also requires the `Ms` capability to be set on the terminal. To confirm it is set for your terminal, run the following inside tmux:

```
tmux info|grep Ms:
```

If it is set you'll receive a response similar to this and no further configuration is needed:

```
 180: Ms: (string) \033]52;%p1%s;%p2%s\a
```

If it shows `[missing]` then it must be set. See [this section](https://github.com/tmux/tmux/wiki/Clipboard#setting-the-ms-capability) in the wiki for more information.

An alternative to using `set-clipboard` is to use an external tool that talks to the local hosts' X server or equivalent to set the clipboard. In Linux this external tool is `xsel` and set with the following configuration directive:

```
set -s copy-command 'xsel -i'
```

`xsel` is available in most common package repositories for installation if you go this route. OSX has a built-in tool called `pbcopy` that can be used. See [this section](https://github.com/tmux/tmux/wiki/Clipboard#external-tools) for more information.

## Using a mouse to select

You can add mouse support for some actions (including copying, scrolling and resizing windows) with the folling:

```
set -g mouse on
```

## Changing the prefix key

If you hate the default prefix key, it can be remapped. To remap keys you'll need to unbind the existing mapping. This example shows the new command as `Ctrl + Space`:

```
unbind C-b
set -g prefix C-Space
```

## Using a plugin manager

Tmux has a plugin manager called, creatively, [Tmux Plugin Manager](https://github.com/tmux-plugins/tpm). Installing it is easy; clone the repository in `~/.config/tmux/plugins` and add the following directive to `tmux.conf`:

```
set -g @plugin 'tmux-plugins/tpm'
```

You can see a list of available plugins [here](https://github.com/tmux-plugins/list). Plugins are added just like the directive for the plugin manager:

```
set -g @plugin 'tmux-plugins/tmux-copycat'
```

Plugins are available for a wide range of uses, including helpers to make tmux a little more friendly with copy paste and search.

___

### References

- [Tmux wiki](https://github.com/tmux/tmux/wiki)
- [Red Hat's beginner's guide to tmux](https://www.redhat.com/sysadmin/introduction-tmux-linux)
- [Man page](https://man.openbsd.org/tmux)
- [Opensource.com's Tmux Cheatsheet](https://opensource.com/downloads/tmux-cheat-sheet?intcmp=701f20000012ngPAAQ)
- [Tmux Plugin Manager README](https://github.com/tmux-plugins/tpm/blob/master/README.md)
