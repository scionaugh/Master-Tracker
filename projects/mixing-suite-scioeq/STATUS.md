# ScioEQ — Parametric EQ (ZDF SVF)

**Last Updated:** 2026-06-15
**Status:** ACTIVE — Prototype + UI v2 complete, parameter tuning next
**Priority:** MEDIUM

## Current Focus
First prototype complete and UI v2 complete (2026-06-12). Cold icy blue-white palette. AbstractFifo spectrum analyser (input + output FFT), resonance detection layer, M/S gradient EQ curve, AnalyserSettingsOverlay (Resolution/Speed/Tilt), legend, band-dot M/S badge. Next: parameter tuning pass and integration testing.

## Next Actions
- Parameter tuning pass (identify unusable zones, consider 15%-extreme-zone mapping)
- Integration testing in a host
- Log bugs and findings for next development cycle
- [PHASE/future] Gain slew on dynamic path (free) + honest mode relabel; optional Vicanek "Natural Phase" matched biquad gated on audibility A/B. No cepstral FIR engine (proven A≡B for cascade). See Research/dynamic_phase/Phase_Implementation_Notes.md

## Blockers
<!-- Leave blank if none -->

## Recent Commits
<!-- Auto-updated on each Mixing Suite git commit -->

## Completed (2026-06-15)
- Factory presets authored (Phase E, 998266c): 9 presets, slug-safe; ScionaughEQPresetTests pass (load + round-trip + neutral rest). Replaces the old single 'Flat' table.
