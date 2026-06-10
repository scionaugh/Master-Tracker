# ScioSpace — Chat Context

## Goals and Purpose
A vertical spatialiser that places sounds convincingly from Tailbone (−90°) to Up The Head (+90°) without timbral damage. The core design principle — no processing on the mid channel, all spatial work on the side channel only — is what allows it to work on dense electronic mixes where conventional HRTF spatializers destroy punch and timbre.

## Key Decisions Made

**Architecture derived from reverse-engineering Plutonium XTra**, not from the original research paper. The paper proposed HRTF high-frequency notching (5–10 kHz); measurement revealed the actual mechanism is low-frequency side-channel phase/amplitude manipulation (5–200 Hz only, flat above).

**Three-component architecture (side channel only, mid untouched):**
1. Low-frequency minimum-phase shelving/resonant network (5–200 Hz) — two cascaded second-order sections, primary lobe ~45–50 Hz, secondary lobe ~100 Hz. Polarity flips between Tailbone and Up The Head.
2. Fixed-level broadband stochastic noise at ~−59.4 dB (signal-relative) — position-independent SR component added to side channel only.
3. Mild position-dependent nonlinearity on side channel — subtle harmonic density shift with body position.

**HRTF notch mechanism is Phase 2 (additive, not replacement).** Pinna-induced spectral notch (4.7–11 kHz, position-dependent) layered over Phase 1 architecture. HRTF Amount knob controls the blend — at 0% the notch is shallow (~−1.5 dB) and timbre is fully preserved; approaching 100% the notch deepens to −6 to −10 dB.

**Seven named body positions:** Up The Head (+90°), Head (+45°), Throat (+20°), Heart/Reference (0°), Stomach (−20°), Lower Belly (−45°), Tailbone (−90°).

**Velvet Noise Decorrelator (VND) is the planned upgrade** for the current Gaussian noise injection — 76–88% fewer CPU operations, no FFT required, multiplier-free.

**Mono-compatibility requirement:** Phase correlation coefficient must remain +0.3 to +0.7. FFT null test (mono sum) must have <0.7 dB ripple across 20 Hz–20 kHz.

## Constraints and Dependencies
- `Research/BST-Spatializer/RESEARCH.md` — primary architecture and psychoacoustics reference
- `Research/BST-Spatializer/Advanced Stereo Widening Techniques Research.pdf` — DSP white paper (M/S, all-pass networks, VND, IACC)
- CIPIC HRTF Database (UC Davis) — for Phase 2 empirical notch calibration
- `UI_Design_V1.md` — XY pad, vectorscope, correlation meter layout

## Open Questions
- Velvet noise upgrade: implement in browser prototype first or go straight to JUCE?
- Phase 2 HRTF notch: use CIPIC database measurements or a parametric notch model?
- Should body position be a continuous XY pad parameter or a discrete stepped selector?
- No SI specification document exists yet for ScioSpace — needs to be written
