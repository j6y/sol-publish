---
publish: true
title: Starship
modified: 2026-04-12T04:36:01.490-04:00
tags:
  - tool/cli
---

> [!abstract] Starship
> A minimal, blazing-fast, and infinitely customizable prompt for any shell.
>
> Documentation: https://starship.rs/guide/
> Repository: https://github.com/starship/starship

```shell
# Windows
winget install --id Starship.Starship
Add-Content -Path $PROFILE "Invoke-Expression (&starship init powershell)"

# Generic linux
curl -sS https://starship.rs/install.sh | sh
echo 'eval "$(starship init bash)"' >> ~/.bashrc

# Arch
pacman -S starship
echo 'starship init fish | source' >> ~/.config/fish/config.fish

# Debian
apt install starship
echo 'starship init fish | source' >> ~/.config/fish/config.fish

# MacOS 
brew install starship
starship init fish | source
echo 'starship init fish | source' >> ~/.config/fish/config.fish
```
