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
- 2026-06-15 · 53ba120 · docs: add public naming convention (codename system)
- 2026-06-15 · 1259f2c · docs: reconcile EQ/MB notes to as-built (EQ presets/SVF; MB OS-gating + sat-stack UI)
- 2026-06-15 · daddec7 · docs: phase-mode research (A=B finding, cramping, Vicanek matched biquad)
- 2026-06-15 · 1a7323e · docs: mark Suite_Fixes (A-H) complete; record final gate + deferred items
- 2026-06-15 · 1068075 · ScioSpace Phase H2: room dropdown + routing toggle + wireframe overlay
