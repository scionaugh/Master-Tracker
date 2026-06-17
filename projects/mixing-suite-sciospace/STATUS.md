# ScioSpace — Spatialiser

**Last Updated:** 2026-06-17
**Status:** ACTIVE — Beta v2 awaiting testing; convolution rooms (beta v3) IMPLEMENTED on develop (Phase H); host tuning next

## Current Focus
VST beta v2 created and installed with stochastic noise implementation and UI updates, awaiting testing. In parallel, the convolution reverb module (beta v3) has a finalized DSP and UI design: juce::dsp::Convolution engine with Mono-Safe/Expanded routing toggle, three prototype IRs generated, hardcoded per-room pre-delay/ducking values, and a wireframe "room" overlay on the XY pad. **Implemented 2026-06-15 (Phase H, commits 4041680 H1 + 1068075 H2):** M/S juce::dsp::Convolution rooms (mid untouched, Mono-Safe mono Δ=6e-8), Expanded ducked mid, per-room predelay/release, 8 ms switch fade, Off bit-identical, 0 latency, ConvolutionTests pass; room dropdown + Mono-Safe/Expanded toggle + puck-centred animated wireframe box (log-sized, solid/dashed by live corr).

## Next Actions
- Test stochastic noise behaviour at varying Aliveness settings (beta v2)
- Test UI updates (beta v2)
- Log bugs and findings for next development cycle
- Confirm reverb wet level (kReverbWet) and wireframe box sizing in a host
- Verify the mono-safe threshold (corr > 0.7) against real mix-bus material
- Source/license additional IRs for the full room library (OpenAIR)

## Blockers
- Mono-safe threshold (corr > 0.7, used for the solid/dashed wireframe indicator) is provisional, needs verification against real mix-bus material
- IR library licensing not yet resolved for any third-party assets beyond the three generated prototypes









## Recent Commits
- 2026-06-17 · aafcd89 · feat(ui): per-instance undo/redo across the suite
- 2026-06-17 · 6054b6d · feat(dsp): playback/export oversampling via isNonRealtime, standardized
- 2026-06-17 · 7edb7e7 · feat(telemetry): consent gating, in-plugin feedback, signal scalars, stability health
- 2026-06-17 · e0c5380 · feat(shared): global ScioSound settings store + shared settings panel
- 2026-06-17 · 4bb81a6 · chore(suite): branch next-beta, bump all plugins to 0.2.0 / β3
