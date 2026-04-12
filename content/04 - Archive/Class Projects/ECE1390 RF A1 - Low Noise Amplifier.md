---
publish: true
title: Low Noise Amplifier for Wifi 802.11a
created: 2025-10-28
modified: 2026-04-11T01:51:47.802-04:00
tags:
  - class/ECE1390RF
  - 📦project
  - analog
  - rf
  - lna
---

## Assets

- Manual: [[ECE1390 RF A1 - Low Noise Amplifier.pdf]]
- Report: [[ECE1390 RF A1 - Report.pdf]]
- Repository: https://gitea.nodusk.me/jay/2025-meng-ece1390-rf

## References

- [[ECE1390 RF R - Report Example.pdf]]
- [[ECE1390 RF L5 - Low Noise Amplifier.pdf]]
- [[RF Microelectronics]]

## Specifications

| **Metric**                     | **Target**             | **Obtained** |
| ------------------------------ | ---------------------- | ------------ |
| **Center Frequency ($f\_{c}$)** | 7 GHz                  | 7.02 GHz     |
| **Bandwidth ($BW$)**           | 100 MHz < BW < 300 MHz | 158 MHz      |
| **S11 to 50$\Omega$ Matching** | < -12 dB               | -26.4 dB     |
| **Gain**                       | > 14 dB                | 14.3 dB      |
| **NF**                         | < 2.1 dB               | 1.1 dB       |
| **Input Referred IIP3**        | > 1 dBm                | 19.8 dBm     |
| **Power Dissipation**          | < 6 mW                 | 5 mW         |

- Direct Conversion Mixer for WiFi 802.11a (WLAN)
- **Input RF Pad**: 60 fF
- **Output Mixer**: 45 fF
- Single-Ended or Differential topology
- **PDK**: [[GF 22nm FDSOI]]
  - mosfets: slvtnfet\_rf\_b and slvtpfet\_rf\_b from cmos22fdsoi\_rf
  - inductor: indp\_mmw from cmos22fdsoi\_mmw
  - series gate inductor ($L\_{g}$) can be off chip (ideal from analogLib)

## Research

The goal of a [[Low Noise Amplifier]] (LNA) is to boost a weak incoming signal while adding as little [[Noise]] as possible. In other words, minimize the ratio of [[Signal-to-Noise Ratio|SNR]] at the input to the SNR at the output, this is called the [[Noise Figure]] (nf).

$$ \text{nf} = \frac{\text{SNR}_{in}}{\text{SNR}_{out}} $$

Transmission lines and antennas are generally designed with 50$\Omega$ [[Output Impedance]] ($R\_{s}$). To minimize reflections at the input to the low noise amplifier at [[Radio Frequency]] (RF), the [[Input Impedance]] ($R\_{in}$) must also be designed to 50$\Omega$. Since the gates of [[Metal-Oxide Field Effect Transistor|MOSFETs]] are [[Capacitor|Capacitive]], the input impedance is large, making traditional amplifier architectures less effective.

One solution is to use a [[Common-Source Amplifier]] and place a [[Shunt Resistor]] at the input to form a resistive matching network. This isn't a great solution as [[Resistor|Resistors]] are very noisy and the [[Noise Figure]] exceeds 3dB, defeating the purpose of a low noise amplifier.

$$ NF \approx 2 + \frac{4 \gamma}{g\_{m}R\_{s}} $$

In a [[Common-Gate Amplifier]] the input is at the source of the input MOSFET, which gives $R\_{in} = 1 / g\_{m}$. Input matching can be achieved by just sizing and biasing the MOSFET appropriately, though it is important to note that $g\_m$ and bias current must be large to achieve 50$\Omega$. When $R\_{s} = R\_{in}$, the noise figure is about 3dB. NF can be further reduced by increasing $g\_m$ even more, sacrificing power and input matching.

$$ NF = 1 + \frac{\gamma}{g\_{m}R\_{s}}  + 4 \frac{R\_{s}}{R\_{1}}\left( 1+\frac{1}{g\_{m}R\_{s}} \right)^2 $$

Another option is an [[Inductor]] degenerated [[Common-Source Amplifier]]. The source degeneration inductor resonates with $C\_{gs}$ of the input MOSFET, which leave a primarily real resistance at the [[Resonance Frequency]]. For perfect matching, the inductor must be large. This problem can be alleviated by adding an off-chip series gate inductor. Noise figure can be low with minimal power consumption, just with slightly worse and more complex input matching.

$$ \text{NF} = 1 + g\_{m}R\_{s}\gamma\left( \frac{\omega\_{0}}{\omega\_{T}} \right)^2 $$
where,

- $\omega\_{0}$ = center frequency
- $\omega\_{T} \approx g\_{m} / C\_{gs}$

source: [[RF Microelectronics]]

## Design

![[lna-transistor-sch.svg#invert|400]]

In this design, a single-stage cascade common-source LNA topology with inductive source degeneration, is chosen to achieve the desired performance metrics. For input matching of the CS LNA, the following must hold true

$$ C\_{gs} = \frac{1}{\omega\_{0}^2 (L\_{1} + L\_{g})} - C\_{pad} $$
$$ g\_{m} = \frac{R\_{in} C\_{gs}}{L\_{1}} (\frac{C\_{gs}+C\_{pad}}{C\_{gs}})^2 $$

Assuming $L\_{1}$ is a bond wire with an inductance of 0.5 $nH$, and $L\_{g}$ is 5 $nH$,

```math
L1 = 0.5 nH
Lg = 5 nH
Cpad = 60 fF
f = 7 GHz
Rs = 50 ohm

Cgs = 1/((2 * pi * f)^2 * (Lg + L1)) - Cpad
gm = (Rs * Cgs)/L1 * ((Cgs + Cpad)/Cgs)^2
ft = gm / ((2 * pi) * Cgs)
```

With a simple parametric analysis, this gm and ft is achieved with a 44$\mu m$/40$nm$ device with 1.6mA of bias current. A channel length of 40nm was used to reduce short channel effects. Putting this design together shows a S11 of -21dB @ 7.2GHz. To reduce the center frequency, the device width can be increased to in turn increase $C\_{gs}$. A width of 50$\mu m$ results in a S11 of -21.4dB @ 7GHz. The bandwidth achieved is 270MHz. Note that increasing the bias current can improve the S11 and bandwidth without adjusting the center frequency, but is unnecessary in this case since matching. May need to reconsider if more output gain is needed.

At the output of the LNA, $L\_{d}$ and $C\_{d}$ form an LC tank resonate to maximize the gain at the desired $f\_{c}$. The LC tank is also in parallel with $C\_{mixer}$ and $C\_{gs}$ $C\_{ds}$ of cascode MOSFET. The latter is assumed to be much smaller and not considered.

```math
Ld = 1.2 nH
f = 7 GHz
Cmixer = 45fF

Cd = 1 / (Ld * (2 * pi * f)^2) - Cmixer
```

The gain is lower than expected, at about 13dB @ 7GHz. As noted earlier, increasing the bias current to 2.5mA improves the gain to above 14.3dB.

| **Component** | **Size**      |
| ------------- | ------------- |
| **Lg**        | 5 nH          |
| **L1**        | 0.5 nH        |
| **Ld**        | 1.2 nH        |
| **Cd**        | 386 nH        |
| **M1-3**      | 50 um / 40 nm |
| **Ib**        | 2.5 mA        |
| **Rb**        | 20 k$\Omega$  |
| **Cp**        | 100 fF        |

## Simulations

### 1. Testbench

The testbench consists of two ports from `analogLib`, 1$\mu F$ ac coupling capacitors, and the mixer load capacitance.

![[lna-testbench-sch.svg#invert|400]]

Port 0

- resistance: 50 Ohms
- source type: sine
  - frequency name 1: RF
  - frequency: frf Hz
  - amplitude 1 (dbm): prf
- display small signal params: select
  - pac magnitude (dBm): prf
  - ac magnitude (Vpk): 1 V

Port 1

- resistance: 50 Ohms
- source type: dc

Variables

- frf = 20G
- frf2 = frf+0.1G
- prf = -50

### 2. S-Parameters

Choosing Analyses

- analysis: sp
  - ports: /PORT0 /PORT1
  - sweep variable: Frequency
  - start: 100M
  - stop: 20G
  - sweep type: log
  - number of steps: 100000
  - do noise: no
  - mode: single-ended

Direct Plot Form

- analysis: sp
  - function: SP
    - plot type: Rectangular
    - modifier: dB20
    - plot S11 (input reflection) and S21 (forward gain)

### 3. IIP3

Choosing Analyses

- analysis: pss
  - fundamental tones: PORT0
  - beat frequency: auto calculate
  - output harmonics: number of harmonics   10
  - accuracy: conservative
  - run transient: yes
  - detect steady state: check
  - stop time: 0.2n
  - sweep: check
  - variable name: prf
  - start: -60
  - stop: 20
  - sweep type: linear
  - number of steps: 20
- analysis: pac
  - input frequency sweep range: Singe-Point
  - freq: frf2
  - maximum sideband: 2

run pss simulation

Direct Plot Form

- analysis: pss
  - function: Compression Point
    - select: Port (fixed R(port))
    - gain compression (dB): 1
    - input power extrapolation point (dBm): -60
    - input referred 1dB compression
    - 1st order harmonic: 1 20G
    - select output port to plot

run pss and pac simulation

Direct Plot Form

- analysis: pac
  - function: IPN Curves
    - circuit input power: variable sweep
    - prf: -60
    - input referred IP3
    - order: 3rd
    - 1st order harmonic: 0 20.1G
    - 3rd order harmonic: -2 19.9G
    - select output port to plot

### 4. Noise

Choosing Analyses

- analysis: pss (setup is same as IIP3 simulation)
  - fundamental tones: PORT0
  - beat frequency: auto calculate
  - output harmonics: number of harmonics   10
  - accuracy: conservative
  - run transient: yes
  - detect steady state: check
  - stop time: 0.2n
  - sweep: check
  - variable name: prf
  - start: -60
  - stop: 20
  - sweep type: linear
  - number of steps: 20
- analysis: pnoise
  - start: 1G
  - stop: 50G
  - sweep type: linear
  - number of steps: 20
  - noise figure: selected
  - output probe instance: /PORT1
  - input port source: /PORT0
  - reference side-band: Enter in field   0

Direct Plot Form

- analysis: pnoise
  - function: Noise Figure
    - plot, use the prf=-60 result

## Results

![[ECE1390 RF A1 - Low Noise Amplifier-1762443056226.png|600]]

processed with [[Pandas]] and [[Matplotlib]], see [git repo](https://gitea.nodusk.me/jay/2025-meng-ece1390-rf/src/branch/main/01-low-noise-amplifier/graph.py)
