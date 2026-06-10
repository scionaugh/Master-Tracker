# Sciotape — Code Context
<!-- ⚠️ CONTEXT UPDATE NEEDED — Code has progressed to beta v2 with stochastic noise + UI updates. Context written before JUCE implementation was underway. -->

## Architecture Overview

```
Plugins/ScionaughTape/Source/
├── PluginProcessor.h/cpp   ← DSP: J-A integrator, bias oscillator, EQ chain, oversampling
└── PluginEditor.h/cpp      ← UI: tape transport animation, B-H loop display, knobs
```

Design doc: `Prototypes/Tape/Tape_Saturator_Design_Outline.md` (21KB, v1.0 — primary handoff doc for JUCE)
SI spec: `SITape.md`
Browser prototype: NOT YET BUILT

## Key Technical Decisions

**Core DSP — Jiles-Atherton hysteresis integrator (stateful, not memoryless):**
- Man = MS * (coth(H/a) - a/H)    [Taylor expand for |H| < epsilon: MS*H/(3*a)]
- dM/dH = (Man - M) / (k*delta - alpha*(Man - M))
- delta tracks sign of dH (NOT sign of H — common implementation error)
- Denominator must be clamped to min absolute value before division

**Six-stage signal chain (order fixed, stages may not be merged):**
1. Record amplifier: Drive + pre-emphasis EQ
2. Upsample 8x
3. Bias injection (AC oscillator summed before J-A — not after)
4. J-A integrator
5. Playback head: dM/dt differentiation + gap loss sinc filter
6. Downsample 8x → playback amplifier (post-EQ + output gain)

**8x oversampling minimum.** Internal rate 352.8 kHz at 44.1 kHz host. Bias oscillator at ~100 kHz = 28% of internal Nyquist.

**5 tape models — 7 parameters each:**
MS (saturation magnetisation), Hc (coercivity), Mr (remanence ratio), a (domain wall density), k (pinning), alpha (inter-domain coupling), Bias (as multiple of Hc), Gap (µm).
Models: Ferric LH, Ampex 456 (primary calibration reference), Scotch 250, Agfa PEM 468, Metal Particle.
All parameter values are calibration starting points — require curve-fitting against Dempwolf et al. 2011 before production.

**Stochastic Injection (from SITape.md):**
- He field perturbation injected before J-A integration, scaling with applied field H
- Particulate noise on M after integration but before playback differentiation
- AC bias noise explicitly excluded

**APVTS parameter IDs:** `model`, `speed`, `drive`, `bias`, `output`, `onset`, `ceiling`, `character`, `knee`, `thickness`, `gap`, `recEq`, `playEq`

**UI components (from UI_Design_V1.md):**
- `TapeTransportComponent` — animated reels (speed → rotation rate), ribbon (drive → glow), head assembly (gap → line width, bias → glow colour temperature)
- `BHLoopComponent` — live Jiles-Atherton loop, updates with physics offset knobs
- Three knob sections: Primary (drive/bias/output), Physics Offsets (onset/ceiling/character/knee/thickness), Machine (gap + EQ toggles)

## Dependencies on Other Projects
- `Shared/StochasticEngine.h` — SI noise system
- `Shared/ScionaughLookAndFeel.h/cpp` — all UI styling
- `SITape.md` — SI injection point spec
- Dempwolf et al. DAFx 2011 — external calibration source for J-A parameters (not in project files)
- `Prototypes/Tube/thermionic_saturator.html` — UI reference for browser prototype

## Known Issues / Technical Debt
- Browser prototype not yet built — J-A architecture unvalidated
- All 5 tape model parameter sets are starting estimates, none calibrated against measured data
- EQ time constant implementation (IEC/NAB biquad chain) not yet scoped for Phase 1

## Open Questions
- Phase 1 bias frequency not finalised — design doc specifies 40–60 kHz at 4x OS
- Phase 1: implement full IEC/NAB EQ chain (all 4 speeds) or simplify to single speed for initial J-A validation?
- Static vs nodal solver question (same as Sciotube): is full J-A integration audibly superior to a simpler approximation at mixing saturation levels?
- Ampex 456 parameter calibration against Dempwolf 2011 — first model to calibrate, all others relative to it
