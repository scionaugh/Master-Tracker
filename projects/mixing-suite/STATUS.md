# Mixing Suite

**Last Updated:** 2026-06-17
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
- 2026-06-17 · 6047db0 · chore: restore AU format on all plugins (VST3 + AU)
- 2026-06-16 · 31e86c5 · fix(compressor): presets no longer pin oversampling (user-owned global)
- 2026-06-16 · 725e487 · chore: build VST3 only (drop AU format from all plugins)
- 2026-06-16 · c967b3a · chore(compressor): add diode + sidechain factory presets; automated QA; flag presets for re-audition
- 2026-06-16 · 52399ee · feat(compressor): diode-bridge model selector + panel
