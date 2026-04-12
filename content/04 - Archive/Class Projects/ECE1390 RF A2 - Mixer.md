---
publish: true
title: Direct Conversion Mixer for WiFi 802.11a
created: 2025-11-18
modified: 2026-04-11T01:53:35.841-04:00
tags:
  - class/ECE1388
  - 📦project
  - analog
  - rf
  - mixer
---

\#🎯Todo

- rewrite design procedure
- write mixer note
- write charge injection note
- write clock feedthrough note

## Assets

- Manual: [[ECE1390 RF A2 - Mixer.pdf]]
- Report: [[ECE1390 RF A2 - Report.pdf]]
- Repository: https://gitea.nodusk.me/jay/2025-meng-ece1390-rf

## References

- [[ECE1390 RF L6 - Mixer]]
- [[RF Microelectronics]]

## Specifications

| **Metric**                            | **Target**        | **Single-Balanced** | **Double-Balanced** |
| ------------------------------------- | :---------------- | :------------------ | :------------------ |
| **IIP3**                              | > 5 dBm           | 5.9 dBm             | 5 dBm               |
| **Conversion Gain**                   | > -7 dB           | -6.9 dB             | -5.2 dB             |
| **Integrated Output Noise**           | < 11$\mu V\_{rms}$ | 7.9 $\mu V\_{rms}$   | 11.2 $\mu V\_{rms}$  |
| **Mixer Power**                       |                   | 0.66 mW             | 1.68 mW             |
| **Local Oscillator Power ($P\_{LO}$)** |                   | -2 dBm              | -2 dBm              |

- Direct Conversion Mixer for WiFi 802.11a (WLAN) Receiver
- **Center Frequency ($f\_{c}$)**: 5.4 GHz
- **IF**: 20 MHz
- **Output Load**: 110 fF
- **Noise Integration BW**: 10kHz - 10MHz
- Singled-Balanced and Double-Balanced topology
- **PDK**: [[GF 22nm FDSOI]]
  - mosfets: slvtnfet\_rf\_b and slvtpfet\_rf\_b from cmos22fdsoi\_rf

## Research

source: [[RF Microelectronics.pdf#page=362|RF Microelectronics - Chapter 6, p.337]]

A [[Mixer]] translates a signal to a different [[Frequency]] by multiplying it with a single tone. In an [[RF Receiver]] (RX), and RF signal is multiplied with a local local oscillator (LO) to produce a baseband or [[Intermediate Frequency]] (IF) signal. In an [[RF Transceiver]] (TX), an IF signal is multiplied with a LO to produce a RF signal. IF is the difference between [[Center Frequency]] ($f\_{RF}$) and LO frequency ($f\_{LO}$), $IF = |f\_{RF} - f\_{LO}|$.

![[Pasted image 20251116023006.png#invert|400]]
[[RF Microelectronics.pdf#page=363&selection=78,0,80,62|RF Microelectronics, p.338]]

The mixer above uses a single-ended RF input and LO, leading to an inefficient LO, where the RF input is discarded for half the time. A more efficient topology is a **single-balanced mixer**, where two differential switches are used that commutate (toggle) the RF signal between two output paths. This topology leads to two main benefits; conversion gain is doubled compared to single-ended topology, and [[Charge Injection]] from the switches is eliminated. Though LO-IF [[Clock Feedthrough]] remains an issue. A **double-balanced mixer** solves this by connecting two single-balanced mixers such that they cancel clock feedthrough at IF port without affecting the IF signal.

![[Pasted image 20251116025217.png#invert|400]]
[[RF Microelectronics.pdf#page=373&selection=297,0,299,61|RF Microelectronics, p.348]]

![[Pasted image 20251116040309.png#invert|200]]
[[RF Microelectronics.pdf#page=374&selection=47,0,49,30|RF Microelectronics, p.349]]

The above are **passive mixers**, where mixing is done without any additional power (apart from $P\_{RF}$ and $P\_{LO}$). Conversion gain with the double-balanced passive mixer is $2 / \pi \approx$ -4dB. **Active mixers** integrate gain and mixing in a single stage with a [[Transconductance]] input. These also come in single-balanced and double-balanced forms, where the gain for both is given by

$$ A\_{v} = \frac{2}{\pi} g\_{m\_{1}} R\_{D} $$

![[Pasted image 20251116124702.png#invert|200]]
[[RF Microelectronics.pdf#page=394&selection=71,0,73,29|RF Microelectronics, p.369]]

![[Pasted image 20251116133414.png#invert|400]]
[[RF Microelectronics.pdf#page=395&selection=174,0,177,40|RF Microelectronics, p.370]]

## Design

single-balanced mixer:
![[single-balanced-mixer-sch.svg#invert]]

double-balanced mixer:
![[double-balanced-mixer-sch.svg#invert]]

In both topologies, the load resistor ($R\_{d}$) is in parallel with the output capacitor ($C\_{load}$). This forms a low pass filter at the IF port whose 3 dB bandwidth must be designed to be larger than IF frequency (20 MHz).

$$ f\_{-3dB} = \frac{1}{2\pi R\_{D}C\_{load}}$$

```math
fif = 20 MHz
Cload = 110 fF

Rdmax = 1/ (fif * 2 * pi * Cload)
```

To begin the design, some rough initial assumptions must be made for the overdrive voltage ($V\_{ov}$) for the mixer transistors, here 0.15V is a safe choice. Output voltage swing is limited to $V\_{DD} - 2V\_{ov}$ in single-balanced, and $\approx V\_{DD}-3V\_{ov}$ in the double-balanced. The can use the latter for both to simplify the designs. Maximum $R\_{d}$ must be decided according to this voltage to ensure the transistors stay in saturation.

$$ R\_{d,max} = \frac{2 V\_{IF,pp}}{I\_{bias}} $$

```math
Vov = 0.2 V
Vdd = 0.9 V
Id1 = 500 uA

Vpp = Vdd - (3 * Vov)
Rd = 2 * (Vpp / Id1)
```

Assuming $I\_{bias}$ = 500 $\mu A$, the maximum $R\_d$ is quite large, showing that there is more than enough voltage headroom for this design. To minimize [[Thermal Nosie]], $R\_{d}$ can be set lower. 100 $\Omega$ is be used for now. The required $g\_{m}$ to meet conversion gain specification (-7 dB) can now be determined by the following

$$ A\_{v} = \frac{2}{\pi} g\_{m\_{1}} R\_{D} $$

```math
Rd = 100 ohm
gain = -7
Av = 10^(gain/20) V/V
gm1 = Av / (Rd * 2/pi)

```

To ensure a good balance between gain and linearity, a $V\_{ov}$ of 0.15 is selected for $M\_{1}$. Below the required $I\_{D1}$ and $W\_1$ is calculated to get the desired $V\_{ov}$.

```math
gm1 = 7 mS
vov = 0.15 V
id = vov*gm1/2


l = 20 nm
kn = 480 uA/(V^2)
W =  (l * 2 * id) / (kn * vov^2) 
```

The best performance was achieved with mixing switches set to a low $V\_{ov}$. This doesn't align with $\frac{g\_{m}}{I\_{D}}$ methodology rules where a low $V\_{ov}$ results better gain but worse noise and linearity. #🎯Todo Need to revisit these simulations.

Both the single- and double-balanced mixers used the same input and switching devices, as well as the same $P\_{LO}$. $I\_{D}$ needed to be increased to achieve the desired gain and linearity.

| **Metric**                      | **Single-Bal.**      | **Double-Bal.** |
| ------------------------------- | :------------------- | :-------------- |
| Input Devices $M\_{1,4}$         | 10$\mu m$/20nm       | 10$\mu m$/20nm  |
| Switching Devices $M\_{2,3,5,6}$ | 30$\mu m$/20nm       | 30$\mu m$/20nm  |
| $R\_D$                           | 100 $\Omega$         | 100 $\Omega$    |
| $I\_{D1}$                        | 588 $\mu A$          | 880 $\mu A$     |
| $M\_b$                           | 3$\mu m$/20nm        | 4.8$\mu m$/20nm |
| $M\_t$                           |                      | 80$\mu m$/20nm  |
| $I\_{ref}$                       | 150 $\mu A$          | 110 $\mu A$     |
| Bias filter $R\_b$, $C\_p$        | 20 k$\Omega$, 100 fF |                 |

## Simulations

### 1. Testbench

![[testbench-sch.svg#invert]]

![[Pasted image 20251119155106.png]]

![[Pasted image 20251119155013.png]]

PORT\_RF Setup

- **resistance**: 50 Ohms
- **source type**: sine
  - **frequency name 1**: FRF
  - **frequency**: frf Hz
  - **amplitude 1 (dbm)**: prf
- **display small signal params**: select
  - **pac magnitude (Vpk)**: 1 V
  - **ac magnitude (Vpk)**: 1 V

PORT\_LO Setup

- **resistance**: 50 Ohms
- **source type**: sine
  - **frequency name 1**: FLO
  - **frequency**: flo Hz
  - **amplitude 1 (dbm)**: plo

PORT\_IF Setup

- **resistance**: 50 Ohms
- **source type**: dc

Variables

- **flo**: 5.4G
- **frf**: flo+20M
- **plo**: 0
- **prf**: -50
- **vlo\_cm**: 0.75
- **vrf\_cm**: 0.55
- **pacmagdb**: prf

### 2. Conversion Gain

- set PORT\_RF **source type** to **dc**.
- set PORT\_RF **pac magnitude (Vpk)** to **1**.

Analyses Setup

- analysis: pss
  - **fundamental tones**: PORT\_LO
  - **beat frequency**: auto calculate
  - **output harmonics**: number of harmonics -> 2
  - **accuracy**: conservative
  - **run transient**: yes
  - **stop time**: 0.3n
  - **sweep**: checked
    - **variable name**: plo
    - **start**: -20
    - **stop**: 40
    - **sweep type**: linear
    - **number of steps**: 20
- analysis: pac
  - **sweeptype**: default
    - **input frequency sweep range**: Single-Point
    - **freq**: frf
  - **maximum sideband**: 2

Direct Plot Form

- analysis: pac
  - **function**: Voltage
    - **select**: Net
    - **sweep**: variable
    - **modifier**: dB20
    - **output harmonic**: -1 20M
    - select output net (**VIF**) to plot

### 3. Noise

- set PORT\_RF **source type** to **dc**.
- set PORT\_RF **pac magnitude (Vpk)** to **1**.

Analyses Setup

- analysis: pss
  - **fundamental tones**: PORT\_LO
  - **beat frequency**: auto calculate
  - **output harmonics**: number of harmonics -> 10
  - **accuracy**: conservative
  - **run transient**: yes
  - **stop time**: 0.3n
  - **sweep**: checked
    - **variable name**: plo
    - **start**: -20
    - **stop**: 40
    - **sweep type**: linear
    - **number of steps**: 20
- analysis: pnoise
  - **Sweeptype**: absolute
  - **start**: 10k
  - **stop**: 10M
  - **sweep type**: linear
  - **number of steps**: 200
  - **maximum sideband**: 10
  - **noise figure**: selected
    - **output**: voltage
    - **positive output node**: /VIF
    - **negative output node**: /GND
    - **input source**: port
    - **input port source**: /PORT\_RF
    - **reference side-band**: Enter in field  -> -1

Direct Plot Form

- analysis: pnoise
  - **function**: Output Noise
  - **units**: V/sqrt(Hz)
  - plot

### 4. IIP3

- set PORT\_RF **source type** to **sine**.
- set PORT\_RF **pac magnitude (dBm)** to **pacmagdb**.

Analyses Setup

- analysis: qpss
  - **fundamental tones**:
    - 1 FLO flo 5.4G Large 3 PORT\_LO
    - 2 FRF frf 5.42G Moderate 3 PORT\_RF
  - **accuracy**: moderate
  - **tstab**: 0.5n
  - **sweep**: checked
    - **variable name**: prf
    - **start**: -70
    - **stop**: 15
    - **sweep type**: linear
    - **number of steps**: 20
- analysis: qpac
  - **sweeptype**: default
    - **input frequency sweep range**: Single-Point
    - **freq**: frf+100k
  - **maximum sideband**: 2

Direct Plot Form

- analysis: qpss
  - **function**: Compression Point
    - **select**: Port (fixed R(port))
    - **gain compression** (dB): 1
    - **input power extrapolation point (dBm)**: -70
    - **input referred 1dB compression**
      - **1st order harmonic**: 20M -1 1
      - select output port to plot
- analysis: qpac
  - **function**: IPN Curves
    - **select**: Port (fixed R(port))
    - **circuit input power**: variable sweep
    - **prf**: -60
    - **input referred IP3**
      - **order**: 3rd
      - **1st order harmonic**: 20.1MM -1 0
      - **3rd order harmonic**: 19.9M 1 -2
      - select output port to plot

## Results

single-balanced mixer:
![[single-balanced-mixer-sim.png|400]]

double-balanced mixer:
![[double-balanced-mixer-sim.png|400]]
