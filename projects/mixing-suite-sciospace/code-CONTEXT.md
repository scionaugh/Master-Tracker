# ScioSpace — Code Context

## Architecture Overview

```
Plugins/ScionaughSpatialiser/Source/
├── PluginProcessor.h/cpp   ← DSP: M/S encode/decode, side-chain processing, noise injection
└── PluginEditor.h/cpp      ← UI: XY pad, vectorscope, correlation meter, knobs
```

Browser prototype: `Research/BST-Spatializer/bst_minimal.html` (Phase 1 built, noise calibration bug outstanding)
Research doc: `Research/BST-Spatializer/RESEARCH.md`
UI spec: `UI_Design_V1.md` (Plugin 3 — Spatialiser section)
SI spec: NOT YET WRITTEN

## Key Technical Decisions

**Signal flow (per sample):**
1. M/S encode: `M = 0.5*(L+R)`, `S = 0.5*(L-R)`
2. Mid passes through untouched
3. Side processing chain:
   - Low shelf biquad ~45 Hz (gain polarity controlled by body position)
   - Peaking biquad ~100 Hz (gain polarity controlled by body position)
   - Allpass biquad ~60 Hz (phase rotation, planned upgrade: 3–4 cascaded stages)
   - Broadband noise at ~−59.4 dB signal-relative (current prototype uses absolute gain — bug)
4. M/S decode: `L = M+S`, `R = M−S`

**FIR crossovers required for side-channel high-pass** (not IIR) — IIR introduces frequency-dependent phase shifts causing image smearing. Confirmed by research paper.

**APVTS parameter IDs:** `pan`, `elevation`, `wideness`, `hrtf`, `noise`, `depth`

**UI components (from UI_Design_V1.md):**
- `XYPadComponent` — X = Pan (−100 to +100), Y = Body Position (−90° to +90°). Puck width scales with `wideness`. Mouse drag updates pan + elevation in real time.
- `VectorscopeComponent` — Lissajous display, persistence tail, 30Hz timer from lockfree circular buffer
- `CorrelationMeterComponent` — horizontal bar −1 to +1, green positive / red negative
- Three `KnobWithLabel`: HRTF Amount, Side Decorrelation Noise, Effect Depth

**Threading:** DSP thread writes L/R samples to `juce::AbstractFifo`-backed circular buffer. UI timer at 30Hz reads for vectorscope. Correlation coefficient computed on DSP thread, written to atomic, read by UI.

## Dependencies on Other Projects
- `Shared/ScionaughLookAndFeel.h/cpp` — all UI styling
- `Research/BST-Spatializer/RESEARCH.md` — architecture reference
- CIPIC HRTF Database — Phase 2 notch calibration

## Known Issues / Technical Debt
- **Noise calibration bug in prototype:** side-channel noise is absolute gain, must be signal-relative to stay below masking threshold at all input levels
- Single allpass biquad insufficient — needs 3–4 cascaded stages (confirmed by research doc)
- No Velvet Noise Decorrelator yet (planned replacement for Gaussian noise)
- No SI specification document written for this plugin

## Open Questions
- VND implementation: browser prototype first or JUCE direct?
- Phase 2 HRTF notch: CIPIC empirical data vs parametric model?
- SI spec needed — noise in ScioSpace serves a different role (stochastic resonance for spatial cues) than in the saturation/compression plugins
