# ScioSee - Analyzer (ScionaughAnalyser)

**Last Updated:** 2026-06-17
**Status:** ACTIVE - Spec + code prompts written, no code yet
**Priority:** MEDIUM

## Current Focus
New suite analyzer plugin. Unified Spectrum / Scope / Stereo as tabs with a persistent loudness meter pinned to the right, built on a NEW shared analysis + visualization layer extracted to `Plugins/Shared/` so upgrades propagate suite-wide. Design and look phase complete (validated with interactive mockups). Spec docs and phased code prompts written to `Plugins/ScionaughAnalyser/` on the `next-beta` branch. No code yet.

## Next Actions
- Run Phase 1 code prompt: SpectrumAnalyzer + SpectrumDisplay extraction, retrofit ScioEQ + ScioMB, with the sine-sweep + pink-noise calibration parity test
- Confirm open decisions: D1 detach default (recommend in-window docking base + true-float opt-in), D2 PLUGIN_CODE (proposed ScSe)
- Then Phases 2-7: scope + ScioGlue, stereo + ScioSpace, loudness module, SignalBus, ScioSee scaffold + filterbank Backend B, snapped/detach layout + bus dropdown
- Final independent subagent audit per the prompts

## Blockers
<!-- Leave blank if none -->

## Recent Commits
<!-- Auto-updated on each Mixing Suite git commit -->

## Completed (2026-06-17)
- All design decisions resolved (name, all-three-displays-at-launch, two-layer shared architecture, extract-first, spectrum dual-engine, signal bus at rollout with discovery dropdown, loudness as new shared module, black theme with colourful signals, tear-off/snapped layout)
- Written to `Plugins/ScionaughAnalyser/`: Design_Scope.md, DSP_Notes.md, UI_Notes.md, ScioSee_CodePrompts.md
- Architecture verified against code on next-beta (greenfield shared modules; extraction sources confirmed in EQ/MB/Glue/Space; SignalBus can reuse ScionaughSignalProbe static pattern)
