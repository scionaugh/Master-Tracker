# ScioSpace — Code Context

## Architecture Overview

```
Plugins/ScionaughSpatialiser/Source/
├── PluginProcessor.h/cpp   ← M/S engine, allpass chain, HRTF, noise, pan, vectorscope
└── PluginEditor.h/cpp      ← XY pad, vectorscope, correlation meter, knobs
```

## Current Implementation (beta v2)

**Signal path (per the header comment):**
```
L/R → M/S encode → side: lowshelf + peaking + 4× allpass → depth blend
      → wideness HPF delta → M/S decode → constant-power pan → output
Mid: optional HRTF notch (5–10.5 kHz) + 13 kHz presence peak
```

**Key implementation details:**

**4 cascaded allpass stages** at 40, 80, 140, 200 Hz (the research doc's recommendation of 3-4 stages is implemented as exactly 4). One-pole IIR allpass: `y[n] = -c*x[n] + x[n-1] + c*y[n-1]`.

**HRTF is implemented (Phase 2 is built):** `midNotch` + `midPresence` filters on the mid channel. 5–10.5 kHz notch + 13 kHz presence peak. The research doc treated this as a future phase — it's in beta v2.

**Sub-bass locking:** Wideness HPF at ~110 Hz keeps sub-bass mono centre. One-pole high-pass on side channel.

**Decorrelation noise:** Pre-generated ring buffer (65536 samples). Signal-relative amplitude set per-block from input RMS — the calibration bug from the browser prototype is fixed.

**Noise is NOT SI-system noise** — the instanceSeed is serialised for suite consistency but the header notes "no stochastic injection." The decorrelation noise is the SR component from the BST architecture, handled separately from the Aliveness system.

**Pan smoothing:** τ ≈ 20ms one-sample smoothing for pan gains — avoids zipper noise on XY pad drag.

**Vectorscope:** SPSC ring buffer (2048 samples) of scopeL/R. `correlationValue` atomic updated per block by DSP thread.

**Parameters (APVTS IDs):** `pan`, `elevation`, `wideness`, `hrtf`, `noise`, `depth`

**5 factory presets.**

**State serialisation:** APVTS state + instanceSeed + telemetry.

## Key Divergences from Design Doc / Research
- HRTF Phase 2 already implemented (was planned as future work)
- Allpass network: exactly 4 stages at fixed frequencies (40/80/140/200 Hz) rather than a variable/sliding network
- No Velvet Noise Decorrelator — still using pre-generated broadband noise ring buffer
- Noise calibration bug from browser prototype (absolute gain) is fixed — now signal-relative

## Dependencies
- `Shared/StochasticEngine.h` (instanceSeed only — no SI injection)
- `Shared/ScionaughLookAndFeel.h/cpp`
- `Shared/ScionaughTelemetry.h/cpp`
- `Research/BST-Spatializer/RESEARCH.md` — architecture reference

## Open Questions
- Testing outcomes from beta v2 pending
- VND (Velvet Noise Decorrelator) upgrade: still worth implementing for CPU efficiency? (current ring-buffer approach works but VND would be ~76% fewer CPU ops)
- HRTF notch frequency mapping: parametric model used — does the midNotch coefficient sweep match the 4.7–11 kHz position-dependent table from the research doc?
- Mono-compatibility: has the FFT null test (<0.7 dB ripple) been validated in beta v2?
