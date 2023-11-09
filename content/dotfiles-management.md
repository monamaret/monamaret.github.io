---
title: Managing dotfiles 
description: Sane defaults for version controlling dotfiles 
tags:
    - dotfiles
---

> The first rule of dotfiles is don't put anything sensitive in them, like passwords or seed words or ssh keys, if you're going to be checking them into version control. The second rule of dotfiles is don't put your entire damn `~./config` directory into version control because that includes a lot of sensitive info and thus violates the first rule.

Okay, let's get serious now that the warnings are out of the way. There are many tools to manage your dotfiles, like [GNU Stow](https://www.gnu.org/software/stow/manual/stow.html), but I use a simple .sh script and a symlink between specific files in my config directory and a separate, sanitized directory I can check into version control without fear named, creatively, `~/dotfiles`. I also name the repository [dotfiles](https://github.com/monamaret/dotfiles) because I like consistency.

## separating ./config and /dotfiles

I am a Linux person and use an Arch-flavored distribution on my main computer. Even if you start managing your dotfiles from a fresh-ish install of something like Manjaro, you'll have a lot of stuff in your `.config` directory you probably don't want to track in version control. So the first step is to figure out what you do want to track. My personal rule is: if I care enough to create a custom configuration, I want to track it in version control. For me this means the absolute minimum is:

- Neovim
- Tmux
- Zsh

### Neovim config

Aside from the LSP config (which [I go into deeper elsewhere](/neovim-lsp/)) probably the most important thing to be aware of regarding tracking Neovim config is that you don't want to check your undo file into version control because sensitive stuff could be in there. When we get to the install script you'll see that it is not symlinked and that is intentional - [it can cause major system problems](https://www.google.com/search?q=neovim+symlink+undo+file). However, the steps the install script takes involves deleting the config directory which contains the undo file, and if you don't re-create it you'll lose the ability to step backwards.  

The primary config file for Neovim is `init.vim` and it lives in the `~/.config/nvim` directory. See [this page](/basic-neovim) for a detailed look at the basics of configuring Neovim.

### Tmux config

If you customize your keyboard commands or change the leader key in Tmux it is probably critical to track your config. See [this page](/tmux-config) for a deep dive on configuring Tmux.

Tmux's config file is named `tmux.conf` and will either be in the user's home directory or `~/.config/tmux`.

### Zsh config

Manjaro ships with an already-customized version of Zsh, and I dislike the prompt and that it uses [a non-supported version of Powerlevel 10k](https://github.com/romkatv/powerlevel10k#arch-linux). So for me the second or third thing after a fresh install is dealing with the Zsh setup. It's helpful to know the order in which Zsh loads config files:

1. `.zshenv`: should only contain the user's environment variables.
2. `.zprofile`: can be used to execute commands after logging in.
3. `.zshrc`: use this to configure the shell itself and for running commands.
4. `.zlogin`: same purpose as `.zprofile` except that it is read after `.zshrc`
5. `.zlogout`: can be used to execute commands when a shell exits

These files live in the user's home directory and are created on first use or install. They can be moved to `~/.config/zsh` and will still be loaded. Not all files must be used. See [this page](/configuring-zsh) for the basics on configuring Zsh.

You might also have some Bash profiles even if you're using Zsh; in Manjaro they're used for terminal colors in X11 applications and situations. You can back them up in the same way as defined for Zsh in the install script - they'll be read from the `~/.config/bash` directory if you move them.

## Install script

The basic principle behind the install script is that the actual files you wish to version control are saved in the `/dotfiles` directory and the script creates a symlink between the file in that directory and the default `~/.config` location. You make changes to the files in `/dotfiles` and initialize the version control repository in the `/dotfiles` directory. [Here's an explainer](https://www.redhat.com/sysadmin/linking-linux-explained) on the difference between hard and symlinks and when you should use which. Hard links can only be created for regular files, not directories, and they can only span a single filesystem.

The structure for a Bash command to create a symbolic link is:

``` { .sh }
ln -s <file path to point to> <new file path>
```

Adding the flag `-f` will **f**orce the creation of the link and remove any existing link or file with the same name before creating the symlink.

Finally: symlinks can only be created on a new pointer, not an existing file. So the steps the script will take in order are:

1. copy the original config file from `~/.config` to the dotfiles directory
2. delete the config file from the `~/.config` directory
3. re-create the application directory within the `~/.config` directory
4. symlink the file from `/dotfiles` to `~/.config`

We delete the directory before re-creating it to prevent the system from complaining and failing the script run.

Each time you add a new entry to the install script, you run it to delete the original configs and create the symlinks. **Don't run the script before copying the configs to the dotfiles directory!** Note also that the script as written here does not include any success statements; it will succeed silently. Run `ls -l` in the config directory to confirm the symlink was set correctly.

### Script example

An example:

``` { .sh }
#!/bin/bash
#
########
# nvim #
########
rm -rf "$HOME/.config/nvim"
mkdir -p "$HOME/.config/nvim"
mkdir -p "$HOME/.config/nvim/undo"
ln -sf "$HOME/dotfiles/nvim/init.vim" "$HOME/.config/nvim"

########
# tmux #
########
rm -rf "$HOME/.config/tmux"
mkdir -p "$HOME/.config/tmux"
ln -sf "$HOME/dotfiles/tmux/tmux.conf" "$HOME/.config/tmux/tmux.conf"
```
