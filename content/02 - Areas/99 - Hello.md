---
publish: true
title: Hello
created: 2026-04-10T22:48:49.900-04:00
modified: 2026-04-11T17:58:09.129-04:00
tags:
  - 🧭map
  - gardenEntry
---

## About This Site

This is a portion of my [[Obsidian]] vault that I use for personal knowledge management. It contains a collection of notes on projects, subjects, and tools. This vault is completely self-written (inspired by [this vault](https://publish.obsidian.md/cynixia/The+Black+Book)). The engineering notes often lack sources, but are devised mainly from textbooks, lecture notes, and other works.

## Find Me

:FabLinkedin: https://www.linkedin.com/in/j6y/
:FabGithub: https://gitea.nodusk.me/explore/repos
:LiMail: contact@j6y.me

## Latest Projects

```base
filters:
  and:
    - note["dg-publish"] == true
    - file.hasTag("📦project")
formulas:
  title: link(file, title)
views:
  - type: list
    name: Recent
    order:
      - formula.title
      - date
      - tags
      - summary
    sort:
      - property: file.mtime
        direction: DESC
    limit: 4
    indentProperties: true
    markers: none

```

## Latest Changes

```base
filters:
  and:
    - note["dg-publish"] == true
    - or:
        - file.inFolder("01 - Projects")
        - file.inFolder("04 - Archive")
formulas:
  title: link(file, title)
views:
  - type: list
    name: Recent
    order:
      - formula.title
      - file.mtime
      - tags
    sort:
      - property: file.mtime
        direction: DESC
    limit: 12
    indentProperties: true
    markers: none
    cardSize: 330

```
