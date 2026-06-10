# Mixing Suite — Code Context

## Architecture Overview

Four JUCE VST3/AU plugins sharing a common Shared/ infrastructure layer. All at beta v2.

```
Plugins/
├── Shared/
│   ├── StochasticEngine.h         ← SI noise system (3 generator types + aliveness curve)
│   ├── HarmonicAnalyzer.h/cpp     ← FFT-based H2–H8 analysis (Sciotube + Sciotape)
│   ├── ScionaughLookAndFeel.h/cpp ← common UI skin
│   ├── ScionaughTelemetry.h/cpp   ← telemetry (consent granted at sign-up)
│   ├── ScionaughVersion.h
│   ├── ScionaughVersionChecker.h
│   └── Fonts/
├── ScionaughTube/Source/
├── ScionaughTape/Source/
├── ScionaughCompressor/Source/
└── ScionaughSpatialiser/Source/
```

## Key Technical Decisions

**StochasticEngine.h — three generator types, all in `namespace si`:**
- `WhiteNoise` — xorshift64* PRNG, flat spectrum
- `BandlimitedWhiteNoise` — two cascaded one-pole lowpasses, configurable cutoff
- `PinkNoise` — Voss-McCartney 16-row algorithm (FET flicker noise)
- Aliveness curve: `drive = (knob/10)^0.4` — compresses useful range into lower travel
- True stereo: L seed = instanceSeed, R seed = ~instanceSeed (bitwise NOT)
- Hard CPU gate: Aliveness == 0 skips all noise arithmetic entirely

**Oversampling:** All four plugins use `juce::dsp::Oversampling` with equiripple FIR, user-selectable Off / 2x / 4x. Two objects pre-built per plugin so switching never allocates on audio thread.

**Threading model:** DSP thread writes to `std::atomic<float>` and ring buffers. UI timer at 30Hz reads and repaints. `paint()` never touches DSP state directly.

**Telemetry:** Consent set to granted at construction — users consent at sign-up on sciosound.com before downloading beta.

**instanceSeed:** Generated at construction, serialised in plugin state. Phase 10 milestone.

## Dependencies on Other Projects
- sciosound.com (LMS): consent and download flow
- scionaugh.com (website): plugin marketing pages

## Known Issues / Technical Debt
- instanceSeed Phase 10: serialisation implemented in Sciotube and Sciotape; verify all four plugins have it
- Phase 10 also noted for compressor and spatialiser

## Open Questions
- Testing outcomes from beta v2 not yet documented
- Which plugins still need Phase 10 instanceSeed serialisation verified?
