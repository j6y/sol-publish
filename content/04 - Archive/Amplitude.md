---
publish: true
aliases:
  - amplitude
  - peak
  - peak-to-peak
  - root-mean-square
  - RMS
title: Amplitude
modified: 2026-04-12T07:15:57.523-04:00
tags:
  - fundamental/signal
---

> [!abstract] Amplitude (A)
> Maximum magnitude or strength of a signal, usually measured from its mean or zero level.

## Types

- Peak ($A\_{p}$): maximum deviation from mean level.
- Peak-to-Peak ($A\_{pp}$): difference between maximum and minimum deviations from mean level.
- Root-Mean-Square ($A\_{rms}$): effective value of a continuous periodic signal.

$$ A\_{rms} = \sqrt{\frac{1}{T} \int^T\_{0} \[f(t)]^2 \ \ dt} $$

## Reference Table

| Waveform          | Peak-to-Peak ($A\_{pp}$) | RMS ($A\_{RMS}$)                                    |
| ----------------- | ----------------------- | -------------------------------------------------- |
| [[Sinusoid]]      | $2 \cdot A\_{p}$         | $\frac{A\_{p}}{\sqrt{2}} \approx 0.707 \cdot A\_{p}$ |
| [[Square Wave]]   | $2 \cdot A\_{p}$         | $A\_{p}$                                            |
| [[Triangle Wave]] | $2 \cdot A\_{p}$         | $\frac{A\_{p}}{\sqrt{3}} \approx 0.577 \cdot A\_{p}$ |

## Relations

| Unit        | Equation                                    |
| ----------- | ------------------------------------------- |
| [[Decibel]] | $\mathrm{dB} = 20\log\_{10} \frac{A}{A\_{ref}}$ |
