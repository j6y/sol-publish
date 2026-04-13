---
publish: true
aliases:
  - power angle
title: Power Angle
modified: 2026-04-12T07:07:14.064-04:00
tags:
  - fundamental/physics
  - parameter
---

> [!abstract] Power Angle ($\phi$)
> Difference between [[Phase|phase]] of [[Phasor|phasor]] representations of [[Voltage|voltage]] and [[Current|current]].
>
> $$ \phi = \theta\_{v} - \theta\_{i} $$
>
> Unit: degree ($^\circ$) or [[radian]] (rad)

## Properties

| Power Factor | Power Angle           | Phase Relation                               | Load                           |
| ------------ | --------------------- | -------------------------------------------- | ------------------------------ |
| Unity        | $\phi = 0$            | $\mathrm{V}$ and $\mathrm{I}$ are in phase   | Purely [[Resistor\|Resistive]] |
| Lagging      | $\phi > 0$            | $\mathrm{I}$ lags $\mathrm{V}$               | [[Inductor\|Inductive]]        |
| Leading      | $\phi < 0$            | $\mathrm{I}$ leads $\mathrm{V}$              | [[Capacitor\|Capacitive]]      |
| Zero         | $\phi = \pm 90^\circ$ | $\mathrm{V}$ and $\mathrm{I}$ are orthogonal | Purely [[Reactance\|Reactive]] |
^properties-table

## Relations

| Parameter               | Formula                    |
| ----------------- | -------------------------- |
| [[Power Factor]]  | $\mathrm{pf} = \cos(\phi)$ |
| [[Complex Power]] | $\angle \mathrm{S} = \phi$ |
| [[Impedance]]     | $\angle \mathrm{Z} = \phi$ |
