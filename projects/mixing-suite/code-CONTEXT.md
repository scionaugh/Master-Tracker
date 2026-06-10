# Mixing Suite — Code Context
<!-- ⚠️ CONTEXT UPDATE NEEDED — Code has progressed to beta v2 with stochastic noise + UI updates. Context written before JUCE implementation was underway. -->

## Architecture Overview

Four separate JUCE plugins sharing a common infrastructure layer:

```
Mixing Suite/
├── Plugins/
│   ├── Shared/                        ← suite-wide shared code
│   │   ├── ScionaughLookAndFeel.h/cpp  ← common UI skin
│   │   ├── HarmonicAnalyzer.h/cpp      ← FFT-based harmonic analysis
│   │   ├── ScionaughTelemetry.h/cpp    ← telemetry/analytics
│   │   ├── ScionaughVersion.h          ← version constants
│   │   ├── ScionaughVersionChecker.h   ← update checking
│   │   ├── StochasticEngine.h          ← SI noise system
│   │   └── Fonts/                      ← Cinzel + Special Elite as BinaryData
│   ├── ScionaughTube/Source/           ← Sciotube JUCE plugin
│   ├── ScionaughTape/Source/           ← Sciotape JUCE plugin
│   ├── ScionaughSpatialiser/Source/    ← ScioSpace JUCE plugin
│   └── ScionaughCompressor/Source/     ← ScioGlue JUCE plugin
├── Prototypes/
│   ├── Tube/thermionic_saturator.html  ← complete browser prototype
│   ├── Tape/                           ← design doc complete, prototype not yet built
│   └── Compressor/Mix Compressor Project/ ← compressor prototype
└── Research/
    └── BST-Spatializer/                ← spatialiser research
```

## Key Technical Decisions

**Shared LookAndFeel (`ScionaughLookAndFeel`):** All four plugins use a single LookAndFeel class. Brass/amber dark aesthetic. Custom `drawRotarySlider()`, `drawLinearSlider()`, `drawButtonBackground()`, `drawComboBox()`. Fonts (Cinzel 700 for titles, Special Elite for labels) loaded as BinaryData via `juce::Typeface::createSystemTypefaceFor()`.

**Threading model (all plugins):** DSP thread writes to `std::atomic<float>` arrays and `juce::AbstractFifo`-backed circular buffers. UI timer at 30Hz reads and calls `repaint()`. `paint()` never accesses DSP state directly.

**APVTS bindings:** All parameters use bare IDs (no prefixes) registered in each processor's `createParameterLayout()`. Bound via `SliderAttachment`, `ButtonAttachment`, `ComboBoxAttachment`.

**Scalable windows:** All editors implement `resized()` with proportional layout. No hardcoded pixel positions. `setResizeLimits()` enforces minimum sizes per plugin.

**Stochastic Injection (SI) system:** `StochasticEngine.h` in Shared. Signal-dependent noise — zero at silence, scales with signal level. Each plugin has a dedicated SI spec doc (SITube.md, SITape.md, SICompressor.md).

**HarmonicAnalyzer:** Shared FFT-based harmonic analysis, used for Sciotube's H2–H8 display and potentially other plugins.

## UI Design Reference
Full spec in `UI_Design_V1.md`. Colour palette, typography, knob/button/meter specs, and per-plugin layouts all defined there. This is the authoritative visual reference for all JUCE editor work.

## Dependencies on Other Projects
None — self-contained suite.

## Known Issues / Technical Debt
- Sciotape browser prototype not yet built (design doc is ready)
- 12AX7 parameter version unresolved (1996 vs 1997 Koren) — blocker for Sciotube JUCE finalisation
- Tube switching in early prototype caused AudioWorklet rebuild — resolved in browser prototype via per-sample parameter reading; JUCE uses AudioParams

## Open Questions
- Which 5 of 11 tube models ship in Sciotube v1? Requires listening validation.
- Is 4x oversampling sufficient for all Sciotube models or does KT88/6C33C require 8x?
- Oversampling: make user-selectable (Off/2x/4x) when project rate ≥ 88.2 kHz?
