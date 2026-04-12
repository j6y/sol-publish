---
publish: true
title: Direct Conversion Mixer for WiFi 802.11a
created: 2025-12-07
modified: 2026-04-11T01:51:58.344-04:00
tags:
  - class/ECE1388
  - 📦project
  - rf
---

## Assets

- Manual: [[ECE1390 RF A3 - Voltage Controlled Oscillator.pdf]]
- Report: [[ECE1390 RF A3 - Report.pdf]]
- Repository: https://gitea.nodusk.me/jay/2025-meng-ece1390-rf

## References

- [[RF Microelectronics]]

## Specifications

| **Metric**                           | **Target** | **$f\_{min}$** | **$f\_{c}$** | **$f\_{max}$** | **Achieved** |
| ------------------------------------ | :--------- | ------------- | :---------- | ------------- | ------------ |
| **Center Frequency ($f\_{c}$) \[GHz]** | 4.9        | 4.5           | 4.89        | 5.26          | 4.89         |
| **Tuning Range \[%]**                 | 15         |               |             |               | 15.6         |
| **Phase Noise @ 100 kHz \[dBc/Hz]**   | <= -92     | -94.8         | -96.6       | -90.4         | -90.4        |
| **Phase Noise @ 1 MHz \[dBc/Hz]**     | <= -117    | -115          | -117.1      | -116.3        | -115         |
| **FOM @ 1 MHz \[dBc/Hz]**             | >= 182     | 179.8         | 182.7       | 182.5         | 179.8        |

- Voltage Controlled Oscillator for WiFi 802.11a (WLAN) Receiver
- **IF**: 20 MHz
- **PDK**: [[GF 22nm FDSOI]]
  - mosfets: slvtnfet\_rf\_b and slvtpfet\_rf\_b from cmos22fdsoi\_rf
  - inductor: indp\_mmw from cmos22fdsoi\_mmw

## Research

A [[Voltage Controlled Oscillator]] (VCO) is an oscillator whose frequency varies in response to an input control voltage. The gain ($K\_{VCO}$) is generally linear in the VCO's tunable range.

[[RF Microelectronics.pdf#page=545&rect=73,495,482,653#invert]]
[[RF Microelectronics.pdf#page=545&selection=68,0,70,75|RF Microelectronics, p.520]]

[[RF Microelectronics.pdf#page=545&selection=115,0,178,59|RF Microelectronics, page 545]]

A popular choice for RF VCO's is [[LC Oscillator|LC Oscillators]] with a [[Varactor]] where the oscillation frequency is determined by

$$ \omega\_{osc} = \frac{1}{\sqrt{ L\_{1}(C\_{1}+C\_{var}) }}$$

## Design

![[voltage-controlled-oscillator-sch.svg]]

An initial estimation of the power budget can be made from the FOM specification (182 dBc/Hz) and target phase noise (-117 dBc/Hz). This comes out to about 7.6 mW, quite large for the requirements.

$$ FOM\_{VCO} = \left( \frac{f\_{0}}{\Delta f} \right)^2\_{|dB} -PN(\Delta f)_{| \frac{dBc}{Hz}} - P_{diss|dBm} $$

```math
FOM = 182
f0 = 4900
df = 1
PN = -117

@[Pdiss::7.593 mW] = 10^((20*log10(f0/df) - PN - FOM)/10) mW
@[Imax::8.436 mA] = Pdiss / 0.9V
```

The oscillation condition of an LC oscillator is given by $g\_{m}R\_{p} \geq 1$, where $g\_{m}$ is the transconductance of the mosfet (M1,M2) and $R\_{p}$ is the parasitic resistance of the inductor (L1,L2). In this pdk, the Q factor is about 16 at 4.9 MHz. The minimum $g\_{m}$ required is about 1 mS.

$$ Q = \frac{\omega L}{R\_{p}} $$

```math
Q = 16
L = 1.5 nH
f0 =4.9 GHz

Rp = Q * L * 2pi * f0
gm = 1/Rp 
```

The required $g\_{m}$ is easily attainable, and a simple parametric sweep can be used to determine the width based on $I\_{D}$ and $g\_{m}$. So in consideration of noise, $I\_{D}$ is selected 1 $mA$ and a $g\_{m} / I\_{D}$ ratio of 20 is targeted. This comes out to a width of 60 $\mu m$.

The total capacitance needed for a 4.9 $GHz$ LC tank with a 1.5 $nH$ inductor is 700 $fF$. Frequency must be tunable to 4.9 $GHz$ $\pm \ 7.5 %$ (4.5325 to 5.2675 GHz). When $C\_{var} \ll C\_{1}$, the range of $C\_{var}$ can be estimated with

$$ \Delta \omega\_{osc} \approx \frac{1}{\sqrt{ L\_{1} C\_{1}}} \frac{C\_{var2}-C\_{var2}}{2C\_{1}} $$

```math
f0 = 4.9 GHz
L = 1.5 nH
C = 1/ ((2pi * f0)^2 * L)

df = 0.15 * f0
dC = 2pi * df * sqrt(L * C) * 2 * C
```

So the CMOS varactors need to have a tunable range of 210 $fF$ between 0-0.8 $V$.
According to the Razavi book, increasing the channel length widens the capacitance range. A trail and error approach shows a range of 239 to 13 $fF$ and thus a delta of 226 $fF$ when varactor is sized to 80$\mu m$/200$nm$. $C\_{1}$ will need some tuning to account for the capacitance offset, but calculating it here isn't worthwhile as there are other parasitics that will become apparent in the simulations.

Initial simulation to verify frequency show that $f\_{c}$ is about 3.93 $GHz$ with a control voltage of 0.46 $V$, which is the midpoint of the varactor. Reducing $C\_{1}$ to 320 $fF$ raises $f\_{c}$ to 4.91 $GHz$. [[#Tuning Range]] simulation shows the range is 5.28 to 4.45 GHz, or 4.865 $GHZ$ $\pm$ 17%. Now some fine tuning finds that 70 $\mu m$ varactor width and 340 $fF$ for $C\_{1}$ achieves 4.86 $GHz$ $\pm$ 15.5%, just a bit closer to the required tuning range.

```math
fmax = 5.23 GHz
fmin = 4.48 GHz

df = fmax - fmin
fc = df/2 + fmin
df_percent = df/fc * 100
```

Running [[#Phase Noise]] simulations show that the noise across the spectrum is 20 $dB$ to high, which is concerning. Attempts to reduce this by playing with mosfet size and $I\_{tail}$ show only marginal improvements. The only remaining way to meet these specifications is the use a small inductor. Not sure why this is the case, Q is much lower so I would expect a higher noise. Anyways, this will mean redoing varactor design AHHHHHHHH. Another note is that noise is minimized at low very $g\_{m} / I\_{D}$.

```math
# -- transconductance --------
Q = 15.6
L = 0.9 nH
f0 = 4.9 GHz

Rp = Q * L * 2pi * f0
gm = 1/Rp 

# -- varactor ----------------
C = 1/ ((2pi * f0)^2 * L)

df = 0.15 * f0
dC = 2pi * df * sqrt(L * C) * 2 * C

# -- tuning results ----------
fmax = 5.26 GHz
fmin = 4.5 GHz

df = fmax - fmin
fc = df/2 + fmin
df_percent = df/fc * 100
```

```math
Pdiss = 0.9 * 7.38

f_0V = 4500
f_0p4V = 4890
f_0p8V = 5260

noise_0V = -115
noise_0p4V = -117.1
noise_0p8V = -116.3

FOM_0V = 20*log10(f_0V) - noise_0V - 10*log10(Pdiss)
FOM_0p4V = 20*log10(f_0p4V) - noise_0p4V - 10*log10(Pdiss)
FOM_0p8V = 20*log10(f_0p8V) - noise_0p8V - 10*log10(Pdiss)
```

A balance was needed between inductor size and varactor size due to the limited Q. This was a bit tricky as small changes could drop the noise performance or fail to converge. Final device sizes in [[ECE1390 RF A3 - Report.pdf]].

## Simulations

![[Pasted image 20251207001714.png]]

### Tuning Range

Analyses Setup

- analysis: pss
  - **engine**: Harmonic Balance
  - **beat frequency**: 4.9G
  - **output harmonics**: number of harmonics -> 10
  - **accuracy**: conservative
  - **tstab**: 6n
  - **oscillator**: checked
    - **oscillator node+**: /VO
    - **oscillator node+**: /VOB
  - **sweep**: checked
    - **variable name**: vctrl
    - **start**: 0
    - **stop**: 0.8
    - **sweep type**: linear
    - **number of steps**: 10

Direct Plot Form

- analysis: pss
  - **function**: Harmonic Frequency
    - select **1** for Harmonic Frequency
    - press plot

### Phase Noise

Analyses Setup

- analysis: pss
  - **engine**: Harmonic Balance
  - **beat frequency**: 4.9G
  - **output harmonics**: number of harmonics -> 10
  - **accuracy**: conservative
  - **tstab**: 6n
  - **oscillator**: checked
    - **oscillator node+**: /VO
    - **oscillator node+**: /VOB
  - **sweep**: unchecked
- analysis: pnoise
  - **sweeptype**: default
  - **relative harmonic**: 1
  - **start**: 100
  - **stop**: 100M
  - **sweep type**: Logarithmic
  - **points per decade**: 10
  - **maximum sideband**: 10
  - **output**: voltage
    - **positive output node**: /VO
    - **negative output node**: /VOB

Direct Plot Form

- analysis: pnoise
  - **function**: Output Noise
  - **units**: dBc/Hz
  - plot

## Results

### Transient

![[Pasted image 20251207175232.png]]

### Range and Noise

![[tuning_range.png]]
![[noise.png]]
