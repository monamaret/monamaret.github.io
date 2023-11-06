---
title: Managing dotfiles 
description: Managing dotfiles
tags:
    - dotfiles
---

> The first rule of dotfiles is don't put anything sensitive in them, like passwords or seed words or ssh keys, if you're going to be checking them into version control.

> The second rule of dotfiles is don't put your entire damn `~./config` directory into version control because that includes a lot of sensitive info and thus violates the first rule.

Okay, let's get serious now that the warnings are out of the way. There are many tools to manage your dotfiles, like [GNU Stow](https://www.gnu.org/software/stow/manual/stow.html), but I use a simple .sh script and a symlink between specific files in my config directory and a separate, sanitized directory I can check into version control without fear named, creatively, `~/dotfiles`. I also name the repository [dotfiles](https://github.com/monamaret/dotfiles) because I like consistency and dislike losing my precious dotfiles and if I have a hard drive disaster or need to set up a new computer I know right where to go to get all my critical configs.

## separating ./config and /dotfiles
