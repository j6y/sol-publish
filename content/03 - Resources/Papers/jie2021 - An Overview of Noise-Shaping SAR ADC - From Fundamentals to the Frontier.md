---
publish: true
aliases:
  - jie2021
title: An Overview of Noise-Shaping SAR ADC - From Fundamentals to the Frontier
modified: 2026-04-12T01:48:36.898-04:00
tags:
  - 🌎resource/paper
  - analog
  - data-conversion
---

| Property    | Details                                                                                    |
| ----------- | ------------------------------------------------------------------------------------------ |
| title       | An Overview of Noise-Shaping SAR ADC - From Fundamentals to the Frontier                   |
| citekey     | jie2021                                                                                    |
| authors     | Lu Jie, Xiyuan Tang, Jiaxin Liu, Linxiao Shen, Shaolan Li, Nan Sun, Michael P. Flynn       |
| url         | https://ieeexplore.ieee.org/document/9569768/                                              |
| doi         | 10.1109/OJSSCS.2021.3119910                                                                |
| attachments | [[jie2021 - An Overview of Noise-Shaping SAR ADC - From Fundamentals to the Frontier.pdf]] |

## Notes

Delta-Sigma ADCs need a fast quantizer to perform oversampling, traditionally flash ADCs perform this role.

Framework

[[SAR ADC]]

Comparator and feedback DAC sample and quantize the input signal. SAR uses comparator output to successively determine $D\_{out}$. Low resolution of SAR will leave a residual error ($V\_{res}$) in the output.

![[00 - Meta/Images/Pasted image 20260409170301.png#invert]]

1. generally CDAC based SAR ADC is used, leaving a residual voltage on the CDAC
2. loop filters sample and process residue
3. filtered residue is summed back into the input

$E\_{s}$ models sampling noise
$E\_{q}$ models quantization error, comparator noise, and DAC settling error
$E\_{d}$ models DAC nonlinearity and mismatch

$H\_{EF}$ (Error-Feedback) and $H\_{CIFF}$ (Cascaded-Integrator Feed-Forward) are loop filters

$E\_{N1}$ and $E\_{N2}$ model input-referred noise of filters

$z^{-1}$ models minimum delay of loop filters

In CIFF path, feedback signal is summed by comparator and not CDAC. Thus acts as an offset

![[00 - Meta/Images/Pasted image 20260409170326.png#invert]]

## Loop Filter

Loop filter is more efficient than that of DT-[[Delta Sigma Modulator|DSM]] since only small residue needs to be processed.

1. filters residue
2. amplifies residue at some frequencies
3. sums output back to SAR

performance strategies

- Passive (filtering and summing)
  - only needs switches and capacitors
  - PVT robust, scaling friendly, energy efficient
  - low gain

- Active

![[00 - Meta/Images/Pasted image 20260409170346.png#invert]]
