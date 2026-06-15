# Mixing Suite

**Last Updated:** 2026-06-15
**Status:** ACTIVE — 7 plugins; 4 original + 2 EQ at v2, ScioMB DSP+UI v1 committed, ScioMB v2 spec'd

## Current Focus
Suite spans seven plugins. The four original plugins (ScioTube, ScioTape, ScioGlue, ScioSpace) are at beta v2 with stochastic noise complete. Both EQs (ScionaughEQ, ScionaughEQP) have prototype + UI v2 complete (2026-06-12). ScionaughMB (multiband) now has DSP (P0-P8) and UI v1 (U0-U5) committed to git (cc69c22); a v2 upgrade spec (4-way dynamics, 3-slot sat stack, threshold-on-bar) is written but uncommitted, with code still lagging the notes. Suite-level next focus: parameter tuning pass and telemetry redesign.

## Next Actions
- ScioMB: finish U6 + run U-phase tests, then build v2 from `ScioMB_v2_CodePrompts.md`
- Parameter tuning pass across all plugins: identify unusable zones, consider 15%-extreme-zone parametric mapping
- Telemetry redesign: session-based snapshots, delta check, retention/purge (fix foundation before event logging)
- ScioEQ/ScioEQP: parameter tuning + integration testing; resolve EQP 12AU7 / LF-freq reconciliations
- Test all plugins and log issues

## Blockers
<!-- Leave blank if none -->











## Recent Commits
- 2026-06-15 · 4041680 · ScioSpace Phase H1: M/S convolution rooms (mid stays untouched)
- 2026-06-15 · 1538a80 · ScioGlue Phase G: numeric GR readout on the GR trace
- 2026-06-15 · 632cfbc · ScioEQP Phase F: reconcile LF selector to 5 positions; mark 200 Hz PROVISIONAL
- 2026-06-15 · 998266c · ScioEQ Phase E: author factory preset set (9 presets)
- 2026-06-15 · efe3464 · ScioTape Phase D: wire latency-compensated dry/wet Mix
