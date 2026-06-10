# Sciotube — Chat Context

## Goals and Purpose
A physically accurate tube saturation plugin based on the Norman Koren SPICE triode/pentode equations (Glass Audio, Vol. 8 No. 5, 1996). The model is fit against published plate curves — not a heuristic waveshaper. Named tube presets are the direct equivalent of Koren's parameter tables.

## Key Decisions Made

**Core model: Koren E1 / plate current equations**
Two equations per sample:
- E1 = (Vp/KP) * log(1 + exp(KP * (1/MU + Vg / sqrt(KVB + Vp^2))))
- Ip = (E1^EX * 2) / KG1 if E1 > 0, else 0

Five parameters (MU, EX, KG1, KP, KVB) fully define each tube's character.

**11 tube models parameterised**

| Tube | MU | EX | KG1 | KP | KVB | Type |
|------|----|----|-----|----|-----|------|
| 12AX7 | 100 | 1.4 | 1060 | 600 | 300 | Triode |
| 12AU7 | 21.5 | 1.3 | 1180 | 84 | 300 | Triode |
| 12AT7 | 60 | 1.35 | 460 | 300 | 300 | Triode |
| 6DJ8 | 28 | 1.3 | 330 | 320 | 300 | Triode |
| 6L6GC | 8.7 | 1.35 | 1460 | 48 | 12 | Pentode |
| EL34 | 11 | 1.35 | 650 | 60 | 24 | Pentode |
| KT88 | 8.8 | 1.35 | 730 | 32 | 16 | Pentode |
| 6550 | 7.9 | 1.35 | 890 | 60 | 24 | Pentode |
| 2A3 | 4.2 | 1.4 | 1500 | 60 | 300 | Power triode |
| 300B | 3.95 | 1.4 | 1550 | 65 | 300 | Power triode |
| 6C33C | 3.1 | 1.4 | 163 | 15 | 300 | Power triode |

**Five controls specified**

| Control | Range | Default | Physical basis |
|---------|-------|---------|----------------|
| Drive | 1x–30x | 3x | Scales Vg into nonlinear region |
| Bias | -1.0 to +1.0 | 0.0 | DC operating point offset; controls H2/H3 ratio |
| Output | -24 dB to +6 dB | -6 dB | Level compensation |
| Mix | 0%–100% | 100% | Parallel dry/wet blend |
| Hi Cut | 2 kHz–22 kHz | Off | Approximates inter-element capacitance rolloff |

**Asymmetry is the mechanism for even-order harmonics.** Symmetric saturation produces odd harmonics only. Bias offset is the physical mechanism for H2 generation.

**Static nonlinearity for prototype.** Plate voltage fixed at 150V. No load-line solver yet.

**Oversampling deferred to JUCE.** AudioWorklet runs at native sample rate; aliasing present at high drive/frequency.

**Browser prototype complete.**
- File: `tube-saturator/index.html`
- File: `tube-saturator/THERMIONIC_SATURATOR.md`
- Koren equations run per-sample inside Web AudioWorklet
- Transfer curve and harmonic bars (H2–H8 via 512-point DFT) update in real time
- Known bug: tube switching causes AudioWorklet rebuild (brief dropout) — needs AudioParams fix in JUCE

## Constraints and Dependencies
- **Stochastic Injection (SI) system:** Two confirmed injection points — grid Johnson/shot noise on EG pre-E1, and multiplicative IP shot noise scaling with sqrt(|IP|). JUCE implementation must be coordinated with SI system; not retrofitted.
- **Parameter source of truth:** Tube.lib, Tube1.lib, errata.txt in the project directory. Any discrepancy with the parameter table above resolves in favour of the library files.
- **Suite-wide Aliveness knob:** Zero-noise-floor and hard-bypass-at-zero decisions from SI system apply here.

## Known Gaps (Browser Prototype → JUCE)
- Fixed plate voltage — needs Newton-Raphson nodal solver per sample
- No oversampling — needs minimum 4x (possibly 8x for KT88/6C33C low-KP models)
- Inter-element capacitances not modelled — needs per-tube IIR chain from CCG, CGP, CCP with gain-dependent Miller effect on CGP
- Grid conduction diode absent — positive-boundary clamping not yet implemented
- No shot noise — needs white noise source with amplitude modulated by instantaneous Ip
- No power supply sag — needs low-pass filtered feedback on effective plate voltage

## Open Questions
- Which 12AX7 parameter set is authoritative — 1996 (KVB=300) or 1997 revision (KVB=8)? This is a blocker for JUCE implementation.
- Which 5 of 11 tubes ship in v1? Requires listening validation; not yet done.
- Static waveshaper vs nodal solver — is the sonic difference audibly significant at mixing saturation levels?
- Oversampling rate: is 4x sufficient or does KT88/6C33C require 8x? Needs aliasing analysis.
- Grid conduction clamping: include or exclude? Adds asymmetric top-end clamping; musical utility vs complexity not yet evaluated.
- Inter-element capacitances (CCG, CGP, CCP): modelled in SPICE library but not confirmed for DSP implementation.
- Where in signal chain does the saturator sit — input, gain-reduction, or output stage?
