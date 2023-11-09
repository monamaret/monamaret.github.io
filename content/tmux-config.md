---
title: Configuring tmux 
description: Basic Tmux configuration 
draft: true
tags:
    - tmux
---

If you want to become a Tmux power user the gold standard reference is of course [the wiki](1) but it can be a bit overwhelming if you're just getting started and are trying to figure out the basics. There are also many good [beginner guides](2) that go deep on why you might want to learn how to use Tmux and what it does for you. This page, however, is going to focus only on how to configure Tmux and some basic helpful tips, like how to copy/paste and how to scroll back through your history.

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

hdaighdhg

## Copy and paste

asihauihdgh

## Using a mouse to select

dhgjkhdg

## Changing the leader key

akhjkhg

## Changing the appearance and colors

ajihgkoahg

## Changing the font

ahdhgoha

## Changing the statusline

aihiohgh

## Using plugins

aoihdiogdhag

## Key binding table

alhgdhgakhg

___

### References

- [1]: [Tmux wiki](https://github.com/tmux/tmux/wiki).
- [2]: [Red Hat's beginner's guide to tmux](https://www.redhat.com/sysadmin/introduction-tmux-linux).
- [3]: [Man page](https://man.openbsd.org/tmux).
- [4]: [Opensource.com's Tmux Cheatsheet](https://opensource.com/downloads/tmux-cheat-sheet?intcmp=701f20000012ngPAAQ).
