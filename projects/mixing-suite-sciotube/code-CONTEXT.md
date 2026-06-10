# Sciotube — Code Context

## Architecture Overview

```
Plugins/ScionaughTube/Source/
├── PluginProcessor.h/cpp   ← DSP: Koren equations, oversampling, SI injection
└── PluginEditor.h/cpp      ← UI: tube visual, transfer curve, THD display, knobs
```

Browser prototype: `Prototypes/Tube/thermionic_saturator.html` (complete, reference for JUCE build)
Design doc: `Prototypes/Tube/THERMIONIC_SATURATOR.md`
SI spec: `SITube.md`

## Key Technical Decisions

**Core DSP — Koren transfer function (per sample, at 4x oversampled rate):**
```
E1 = (Vp/KP) * log(1 + exp(KP * (1/MU + Vg/sqrt(KVB + Vp^2))))
Ip = (E1^EX * 2) / KG1   if E1 > 0, else 0
```
- Vg = input_sample * drive + bias * 2
- Vp = 150V fixed (static nonlinearity — no load-line solver yet)
- Output = Ip - Ip_dc (static DC centering + one-pole DC blocker at ~10Hz for residual DC from asymmetric saturation)
- Normalised so unity drive ≈ unity gain

**4x oversampling (in prototype; JUCE to match):**
- 64-tap windowed-sinc FIR (Blackman kernel), 4 polyphase branches
- Group delay: 60 high-rate samples = 15 host-rate samples
- Dry path delay-matched (15 samples) for phase-coherent Mix blending
- Measured aliasing at -144 to -191 dB with 5kHz tone at Drive 20 into 6C33C
- `OS` constant drives all derived values (filter length, polyphase split, dry delay)
- JUCE option: `juce::dsp::Oversampling` module can replace polyphase FIR

**11 tube models — parameters from Tube.lib, Tube1.lib, errata.txt:**
See chat-CONTEXT.md for full parameter table.

**Stochastic Injection (from SITube.md):**
- Grid noise: bandlimited white noise added to EG before E1 computation. Amplitude scales with |input|. Rolled off at ~8kHz at -12dB/oct (first-order IIR). Range: 0.5–5µV RMS referred to input.
- Shot noise: multiplicative on IP after transfer function. Scales with sqrt(|IP|). White noise, no injection-point filtering. Range: 0.01–0.1% of quiescent IP.
- Both driven by shared Aliveness knob via `StochasticEngine`. Both zero at silence.
- Evaluate Aliveness conditional before noise arithmetic in per-sample loop.
- IP_out = IP * (1 + noise_sample * drive_scaled)
- sqrt(|IP|) must clamp to zero before sqrt to avoid NaN.

**APVTS parameter IDs:** `tube`, `drive`, `bias`, `output`, `mix`, `hicut`

**UI components (from UI_Design_V1.md):**
- `TubeDisplayComponent` — animated tube shape (4 categories), amber glow cycles 0.5→1.0 opacity on ~2.4s sine, 30Hz timer
- `TransferCurveComponent` — live Koren curve, normalised, updates on drive/bias/tube changes
- `THDDisplayComponent` — H2–H8 bars from HarmonicAnalyzer shared component, bar opacities breathe on 3s staggered sine
- 5 `KnobWithLabel` components bound to APVTS
- Threading: FFT → 7 atomic floats (H2–H8) → UI timer (30Hz) → repaint

## Dependencies on Other Projects
- `Shared/StochasticEngine.h` — SI noise system
- `Shared/HarmonicAnalyzer.h/cpp` — H2–H8 computation
- `Shared/ScionaughLookAndFeel.h/cpp` — all UI styling
- `SITube.md` — SI calibration targets and implementation notes

## Known Issues / Technical Debt
- Static plate voltage (Vp = 150V) — planned upgrade: Newton-Raphson nodal solver per sample
- Inter-element capacitances simplified to single Hi Cut knob — planned: per-tube IIR from CCG, CGP, CCP with gain-dependent Miller effect on CGP
- Grid conduction diode not yet implemented — positive-boundary clamping absent
- No power supply sag — planned: low-pass filtered feedback on effective plate voltage

## Open Questions
- Which 12AX7 parameters are authoritative: 1996 (MU=100, KP=600, KVB=300) vs 1997 revision (MU=107.5, KP=549, KVB=8)? Must resolve before JUCE finalisation — KVB=8 vs 300 produces meaningfully different transfer functions.
- Which 5 of 11 tubes ship in v1? Requires listening session across full Drive/Bias range.
- 4x vs 8x oversampling — is 8x needed for KT88/6C33C (KP=32/15)? Needs aliasing analysis.
- Grid conduction diode: include or exclude? Musical utility vs complexity not evaluated.
- Make oversampling user-selectable when project rate ≥ 88.2 kHz?
