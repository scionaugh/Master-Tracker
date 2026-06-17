# Mixing Suite — Code Context

## Architecture Overview

Seven JUCE VST3/AU plugins (ScioSee/ScionaughAnalyser is a design-only 8th) sharing a common Shared/ infrastructure layer. Whole suite is at next-beta 0.2.0/β3 on branch `next-beta`.

```
Plugins/
├── Shared/
│   ├── StochasticEngine.h         ← SI noise system (3 generator types + aliveness curve)
│   ├── HarmonicAnalyzer.h/cpp     ← FFT H2–H8 + (next-beta) centroid/flatness/even-odd + running avg
│   ├── TransformerSat.h           ← selectable output-transformer voicing (WDF)
│   ├── ScionaughLookAndFeel.h/cpp ← UI skin; (next-beta) metal knob + MetalPalette/setMetalPalette/drawFaceplate
│   ├── ScionaughTelemetry.h/cpp   ← telemetry; sendEvent→EVENTS_ENDPOINT(/feedback); consent read from settings
│   ├── ScionaughSettings.h        ← (next-beta) global JSON store + gear SettingsPanel + SettingsBridge + exportChoiceIndex
│   ├── ScionaughFeedback.h        ← (next-beta) in-plugin feedback modal + payload sender
│   ├── ScionaughSignalProbe.h     ← (next-beta) in/out meters, NaN/Inf, CPU, clean-exit marker, in+out analysers
│   ├── ScionaughUndo.h            ← (next-beta) UndoCoordinator (gesture→transaction) + UndoBar
│   ├── ScionaughBehavior.h        ← (next-beta) behavioural/preset events + bypass reporter
│   ├── ScionaughVersion.h         ← kBetaNumber/kBetaLabel + kStateVersion
│   ├── ScionaughVersionChecker.h  ← notify banner; (next-beta) opt-in staged auto-update download
│   └── Fonts/
├── ScionaughTube  ScionaughTape  ScionaughCompressor(ScioGlue)  ScionaughSpatialiser
├── ScionaughEQ  ScionaughEQP  ScionaughMB    (each .../Source/)
└── ScionaughAnalyser/  (ScioSee — design/prompts only, no code)
```

**next-beta shared patterns (identical in all 7):** consent via `ScionaughSettingsBridge` (no hard-coded setConsentGranted); each processor owns a `ScionaughSignalProbe` (begin/endBlock bracket processBlock, prepared in prepareToPlay), declares `juce::UndoManager undoManager` BEFORE apvts and passes `&undoManager` to the APVTS ctor with a `ScionaughUndoCoordinator` after, exposes `getTelemetry()`, and emits ScionaughBehavior events. OS plugins pre-prepare all factors and pick per-block via `isNonRealtime() ? exportChoiceIndex(...) : playbackParam` (never initProcessing in processBlock). Editors carry gear/feedback/Undo chrome + call `setMetalPalette({hero ramp})`. Metal KNOB form is on the 4 hardware plugins; EQ/EQP/MB keep compact arc-knobs but take the colour.

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
