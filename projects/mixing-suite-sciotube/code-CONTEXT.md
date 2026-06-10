# Sciotube — Code Context

## Architecture Overview

```
Plugins/ScionaughTube/Source/
├── PluginProcessor.h/cpp   ← Koren DSP, oversampling, SI injection, presets, state
└── PluginEditor.h/cpp      ← Tube visual, transfer curve, THD display, knobs
```

## Current Implementation (beta v2)

**Koren transfer function — full JUCE implementation:**
```cpp
E1 = (Vp/KP) * softplus(KP * (1/MU + Vg/sqrt(KVB + Vp^2)))
Ip = E1^EX * 2 / KG1   if E1 > 0, else 0
```
- softplus replaces log/exp for numerical stability: guards x > 20 (return x) and x < -20 (return exp(x))
- Vp = 150V fixed
- EG perturbation added pre-E1; shot noise multiplicative on (ip - ip_dc)
- DC blocker: one-pole y = x - x1 + 0.9986*y1 (~10 Hz)

**Oversampling:** Off / 2x / 4x via `juce::dsp::Oversampling` equiripple FIR. Default 4x. Latency reported to host and dry path delay-matched via `DryWetMixer.setWetLatency()`.

**Stochastic Injection:**
- gridNoise: `BandlimitedWhiteNoise`, 8 kHz cutoff, scales with |input|
- shotNoise: `WhiteNoise`, multiplicative on (ip - ip_dc), scales with sqrt(|ip - ip_dc|)
- kCeilingGrid = 1e-6f (~1 µV), kCeilingShot = 0.0005f (0.05% quiescent IP)
- One noise draw per native sample, held constant across oversampled sub-steps

**Parameters (APVTS IDs):** `tube`, `drive`, `bias`, `output`, `mix`, `hicut`, `oversample`, `aliveness`

**Note on oversample param:** Added in beta v2 — design doc specified fixed 4x. Now user-selectable Off/2x/4x, default 4x.

**Note on aliveness param:** Added in beta v2 — not in original design doc. Exposes SI depth as a real-time automatable control.

**5 factory presets:**
| Preset | Tube | Drive | Bias | Notes |
|--------|------|-------|------|-------|
| Bass | 300B | 5.0 | +0.2 | SET warmth, smooth H2 |
| Snare | EL34 | 8.0 | -0.15 | British upper-mid edge |
| Hats | 12AU7 | 2.5 | 0.0 | Transparent, minimal colour |
| Drums | 12AX7 | 6.0 | +0.1 | Rich H2, musical density |
| Leads | EL34 | 10.0 | -0.2 | Presence + odd-harmonic bite |

**12AX7 parameters used:** 1996 version (MU=100, EX=1.4, KG1=1060, KP=600, KVB=300) — the version question from the design docs was resolved in favour of the 1996 Koren library values.

**HarmonicAnalyzer:** Feeds H2–H8 bar display in editor.

**State serialisation:** APVTS state + instanceSeed + telemetry (Base64 encoded).

## Dependencies
- `Shared/StochasticEngine.h`
- `Shared/HarmonicAnalyzer.h/cpp`
- `Shared/ScionaughLookAndFeel.h/cpp`
- `Shared/ScionaughTelemetry.h/cpp`

## Known Issues / Technical Debt
- Fixed plate voltage (Vp = 150V) — nodal solver still planned for future version
- Inter-element capacitances: Hi Cut knob is still a single manual approximation

## Open Questions
- Testing outcomes from beta v2 pending
- Does instanceSeed serialisation survive a DAW project reload correctly?
