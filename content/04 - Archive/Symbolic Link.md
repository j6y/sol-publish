---
publish: true
aliases:
  - symbolic link
title: Symbolic Link
modified: 2026-04-12T03:00:22.170-04:00
tags:
  - tool/cli
  - tool/filesystem
---

> [!abstract] Symbolic Link
> Pointer in a filesystem to another file or folder.

```shell
# Windows
New-Item -ItemType SymbolicLink -Path "<link-path>" -Target "<actual-path>"

# Linux/MacOS
ln -s "<actual-path>" "<link-path>"
```
