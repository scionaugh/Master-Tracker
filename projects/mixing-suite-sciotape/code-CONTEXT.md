# Sciotape — Code Context

## Architecture Overview

```
Plugins/ScionaughTape/Source/
├── PluginProcessor.h/cpp   ← J-A DSP, oversampling, gap filter, SI injection, presets
└── PluginEditor.h/cpp      ← Tape transport animation, B-H loop display, knobs
```

## Current Implementation (beta v2)

**Jiles-Atherton integrator — full JUCE implementation:**
- Splits irreversible magnetisation (Mirr) from the reversibility blend
- Total M = (1 - revC) * Mirr + revC * Man
- Character knob maps to reversibility coefficient revC — higher Character = lower revC = squarer loop
- hePerturb added to He before integration (stochastic injection)
- coth singularity guarded in jaStep

**AC bias — transfer-function approach, NOT a literal oscillator:**
- Original design doc specified a literal ultrasonic oscillator summed before J-A
- Implemented differently: bias shifts the reversibility blend in buildEffectiveParams
- Overbias adds a one-pole HF roll-off (one pole per channel, native rate, post gap)
- This avoids aliasing issues that would arise from a sampled ultrasonic tone through the J-A nonlinearity

**Gap loss:** Biquad LPF approximating sinc gap loss. Applied post-downsample at native rate. Recomputed only when gap (µm) or tape speed changes.

**Oversampling:** Off / 2x / 4x via `juce::dsp::Oversampling` equiripple FIR. Default 4x.

**5 tape models with calibrated parameters:**
| Model | MS | Hc | Mr | a | k | alpha | biasNom | gap |
|-------|----|----|----|----|---|-------|---------|-----|
| Ferric LH | 320 | 240 | 0.78 | 1400 | 440 | 2.0e-4 | 0.65 | 2.0µm |
| Ampex 456 | 500 | 310 | 0.86 | 920 | 360 | 1.6e-4 | 0.72 | 2.5µm |
| Scotch 250 | 460 | 280 | 0.83 | 1050 | 380 | 1.7e-4 | 0.70 | 2.3µm |
| PEM 468 | 540 | 330 | 0.89 | 820 | 320 | 1.3e-4 | 0.75 | 2.8µm |
| Metal Particle | 580 | 350 | 0.91 | 780 | 300 | 1.1e-4 | 0.77 | 3.0µm |

**Stochastic Injection:**
- heNoise: `BandlimitedWhiteNoise`, scales with |He| — field perturbation pre-integration
- particulateNoise: `WhiteNoise`, multiplicative on replayed M
- kCeilingHe = 0.003f (0.3% of Hc), kCeilingM = 0.002f (0.2% of Ms)

**Parameters (APVTS IDs):** `model`, `speed`, `drive`, `bias`, `output`, `onset`, `ceiling`, `character`, `knee`, `thickness`, `gap`, `recEq`, `playEq`, `oversample`, `aliveness`

**B-H loop display:** `computeBHLoop()` drives the J-A core with a sinusoidal field sweep to its steady-state limit cycle. Renders the exact audio model, not an approximation. 400 points.

**8 factory presets.**

**State serialisation:** APVTS state + instanceSeed + telemetry.

## Key Divergences from Design Doc
- AC bias is a transfer-function model, not a literal oscillator — oscillator approach removed intentionally (avoids aliasing from sampled ultrasonic tone through J-A)
- Oversampling: design doc specified 8x minimum (driven by oscillator Nyquist requirement). With oscillator removed, 8x requirement no longer applies — code implements Off/2x/4x matching the rest of the suite
- Oversampling is user-selectable (Off/2x/4x, default 4x) — design doc specified fixed rate
- IEC/NAB EQ chain: recEq/playEq parameters exist but implementation status TBC from testing
- No separate record/playback amplifier stages as originally specced — simplified to drive + output gain

## Dependencies
- `Shared/StochasticEngine.h`
- `Shared/HarmonicAnalyzer.h/cpp`
- `Shared/ScionaughLookAndFeel.h/cpp`
- `Shared/ScionaughTelemetry.h/cpp`

## Open Questions
- Testing outcomes from beta v2 pending
- IEC/NAB EQ chain: what is current implementation status of recEq/playEq?
- Tape model parameter calibration against Dempwolf 2011 — still outstanding
