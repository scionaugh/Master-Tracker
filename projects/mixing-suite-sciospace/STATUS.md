# ScioSpace — Spatialiser

**Last Updated:** 2026-06-11
**Status:** ACTIVE — Beta v2 awaiting testing; convolution reverb module (beta v3) design finalized, ready for implementation

## Current Focus
VST beta v2 created and installed with stochastic noise implementation and UI updates, awaiting testing. In parallel, the convolution reverb module (beta v3) has a finalized DSP and UI design: juce::dsp::Convolution engine with Mono-Safe/Expanded routing toggle, three prototype IRs generated, hardcoded per-room pre-delay/ducking values, and a wireframe "room" overlay on the XY pad. Ready to move into implementation.

## Next Actions
- Test stochastic noise behaviour at varying Aliveness settings (beta v2)
- Test UI updates (beta v2)
- Log bugs and findings for next development cycle
- Load the three prototype IRs into the JUCE project and verify playback through the convolution engine
- Build the M/S routing wrapper around juce::dsp::Convolution
- Build the wireframe room box overlay on the XY pad

## Blockers
- Mono-safe threshold (corr > 0.7, used for the solid/dashed wireframe indicator) is provisional, needs verification against real mix-bus material
- IR library licensing not yet resolved for any third-party assets beyond the three generated prototypes

## Recent Commits
<!-- Auto-updated on each Mixing Suite git commit -->
