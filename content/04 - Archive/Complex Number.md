---
publish: true
aliases:
  - complex number
  - complex conjugate
title: Complex Number
modified: 2026-04-12T07:18:33.689-04:00
tags:
  - fundamental/math
---

> [!abstract] Complex Number (z)
> Extension of [[Real Number|real numbers]] using the imaginary unit $j = \sqrt{ -1 }$.
>
> $$ z = a + jb = |z|e^{j\phi} $$

See also: [[Power Factor]] and [[Power Angle]]

## Representations

| Form         | Notation                                  | Components                                                    |
| ------------ | ----------------------------------------- | ------------------------------------------------------------- |
| Cartesian    | $z = a + jb$                              | Real: $a = \mathrm{Re}(z)$<br>Imaginary: $b = \mathrm{Im}(z)$ |
| Polar/Phasor | $z = |z| e^{j\phi} = |z| \angle \phi$ | Magnitude: $|z|$<br>Phase: $\phi$                           |

**Cartesian → Polar**

- $|z| = \sqrt{a^2 + b^2}$
- $\phi = \tan^{-1}(b/a) \ \ \ \text{quadrant aware}$

**Polar → Cartesian** (using [[Euler’s Formula]])

- $a = |z| \cos \phi$
- $b = |z| \sin \phi$

## Operations

| Operation                | Formula                                                                |
| ------------------------ | ---------------------------------------------------------------------- |
| Addition and Subtraction | $z\_{1} \pm z\_{2} = (a{1} \pm a\_{2}) + j(b\_{1} \pm b\_{2})$              |
| Multiplication           | $z\_{1}z\_{2} = |z\_{1}||z\_{2}| \angle(\phi\_{1} + \phi\_{2})$          |
| Division                 | $z\_{1} / z\_{2} = |z\_{1}| / |z\_{2}| \ \angle (\phi\_{1} - \phi\_{2})$ |
| Inversion                | $1 / z = z^\* / |z|^2 = 1 / |z| \ \angle -\phi$                     |
| Conjugate                | $z^\* = a - jb$                                                         |
