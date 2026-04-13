---
publish: true
title: Chezmoi
modified: 2026-04-12T04:36:24.703-04:00
tags:
  - tool/cli
---

> [!abstract] Chezmoi
> Open-source command-line tool for managing your personal “dotfiles” and configuration files across multiple machines.
>
> Documentation: https://www.chezmoi.io/
> Repository: https://github.com/twpayne/chezmoi

---

## Setup

1. Install Chezmoi.

```shell
# Windows
winget install twpayne.chezmoi

# Arch
sudo pacman -S chezmoi

# Ubuntu
snap install chezmoi --classic

# MacOS
brew install chezmoi
```

2. Initialize a new or existing repository.

Existing Repository:

```bash
chezmoi init <dotfile-repo>
chezmoi -v apply

# <dotfile-repo> ex. https://gitea.nodusk.me/jay/dotfiles.git
```

New Repository:

```bash
chezmoi init
cd ~/.local/share/chezmoi

git init
git add .
git commit -m "inital commit"
git remote add origin git@github.com:<github-username>/dotfiles.git
git push -u origin main
```

---

## Usage

- default directory: `~/.local/share/chezmoi`

```bash
# chezmoi commands (reference)
chezmoi add <path> # add new directory or file from chezmoi
chezmoi forget <path> # remove directory or file from chezmoi
chezmoi edit <path> # edit file (or just edit some other way)
chezmoi diff # view changes
chezmoi -v apply # print log and apply changes
chezmoi -v -n apply # print log
chezmoi init <git-url> # initialize dotfiles from git
chezmoi merge <file> # merge conflicts from local and remote
chezmoi update -v # pull and apply changes from remote repo
```
