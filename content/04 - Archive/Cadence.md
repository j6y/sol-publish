---
publish: true
title: Cadence
created: 2026-04-11T17:31:27.460-04:00
modified: 2026-04-11T18:00:22.271-04:00
tags:
  - tool/eda
  - cadence
---

> [!abstract] Cadence
> An industry-standard Electronic Design Automation (EDA) platform used for the full-flow design of custom analog, mixed-signal, and RF integrated circuits (ICs).

```base
filters:
  and:
    - file.hasTag("cadence")
views:
  - type: list
    name: Notes
    order:
      - file.name
      - file.mtime
      - tags
    sort:
      - property: file.mtime
        direction: DESC
    indentProperties: true

```
