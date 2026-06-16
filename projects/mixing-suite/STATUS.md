# Mixing Suite

**Last Updated:** 2026-06-16
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
- 2026-06-16 · 9d1760f · fix(compressor): apply attack once (instant-peak detector + single gain pole) so the ms knob is accurate
- 2026-06-16 · eb4c666 · docs(compressor): spec attack-fix + external sidechain + diode-bridge; add ScioGlue test scaffold
- 2026-06-15 · 2b118f0 · refactor(telemetry): session snapshots + delta send + retention cap + sendEvent foundation
- 2026-06-15 · c5d0089 · fix(naming): drop old-name->codename slug map (IP leak); rely on index-stable restore
- 2026-06-15 · fe9eba9 · feat(transformer): selectable voicing on ScioGlue + ScioEQ + ScioEQP via shared TransformerSat
