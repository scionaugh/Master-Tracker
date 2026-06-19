# ScioSpace — Spatialiser

**Last Updated:** 2026-06-19
**Status:** ACTIVE — Beta v2 awaiting testing; convolution rooms (beta v3) IMPLEMENTED on develop (Phase H); host tuning next

## Current Focus
VST beta v2 created and installed with stochastic noise implementation and UI updates, awaiting testing. In parallel, the convolution reverb module (beta v3) has a finalized DSP and UI design: juce::dsp::Convolution engine with Mono-Safe/Expanded routing toggle, three prototype IRs generated, hardcoded per-room pre-delay/ducking values, and a wireframe "room" overlay on the XY pad. **Implemented 2026-06-15 (Phase H, commits 4041680 H1 + 1068075 H2):** M/S juce::dsp::Convolution rooms (mid untouched, Mono-Safe mono Δ=6e-8), Expanded ducked mid, per-room predelay/release, 8 ms switch fade, Off bit-identical, 0 latency, ConvolutionTests pass; room dropdown + Mono-Safe/Expanded toggle + puck-centred animated wireframe box (log-sized, solid/dashed by live corr).

## Recent Fixes
- **2026-06-17 · Pan insertion-loss bug FIXED (code, not yet rebuilt).** Reported as a noticeable volume drop the moment ScioSpace is inserted, present even at WIDENESS 0. Root cause: the pan stage used a constant-power law (cos/sin^γ, γ=1.494) intended for mono sources, applied as a master gain to the already-decoded stereo output — so centre pan (the default) multiplied both channels by 0.596 = **−4.5 dB on every insert**, independent of wideness/depth/HRTF/noise/reverb. Replaced with a stereo **balance** law: centre = unity (0 dB) on both channels, panning attenuates only the opposite channel, hard pan = silence on the far channel. Verified numerically (centre 0.00 dB; extremes unchanged). Change is confined to the pan-coefficient block in `processBlock` (PluginProcessor.cpp); ScioSpace-only — the pattern is not present in the other plugins. **Needs a macOS rebuild to ship — not yet compiled/installed.**

## Next Actions
- **Rebuild ScioSpace on macOS** to ship the pan balance-law fix (code done, binary not yet built)
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
- 2026-06-19 · 6093270 · feat(suite): UI Rounds 6 + 7 — shared control plate, recomposes, EQP sliders, blue gonio
- 2026-06-19 · 10f9490 · feat(suite): UI Round 5 + 5.2 + ScioSee block-mode spectrum rework
- 2026-06-18 · 0fe0313 · feat(suite): next-beta UI cohesion (rounds 1–3) + Dynamic-EQ/Totalyser DSP + ScioSee/ScioClip
- 2026-06-17 · be15c62 · fix(ui): dark-desaturated ScioGlue red + compact metal knob for EQ/EQP/MB
- 2026-06-17 · f62dbfb · chore(suite): next-beta final audit report
