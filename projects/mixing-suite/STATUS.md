# Mixing Suite

**Last Updated:** 2026-06-17
**Status:** ACTIVE — next-beta (v0.2.0/β3) unified build specced + autonomous runner ready on branch `next-beta`

## Current Focus
Suite spans seven plugins. The four original plugins (ScioTube, ScioTape, ScioGlue, ScioSpace) are at beta v2 with stochastic noise complete. Both EQs (ScionaughEQ, ScionaughEQP) have prototype + UI v2 complete (2026-06-12). ScionaughMB (multiband) now has DSP (P0-P8) and UI v1 (U0-U5) committed to git (cc69c22); a v2 upgrade spec (4-way dynamics, 3-slot sat stack, threshold-on-bar) is written but uncommitted, with code still lagging the notes. Suite-level next focus: parameter tuning pass and telemetry redesign.

## Next Actions
- NEXT-BETA (v0.2.0 / β3): unified suite build specced (`docs/next_beta_unified_spec.md`) with an autonomous Opus runner (`docs/prompts/next_beta_runner.md`) on branch `next-beta`. Scope: shared settings page, telemetry + in-plugin feedback + signal scalars + stability, playback/export oversampling, undo/redo, metal visual pass + per-plugin whole-metal colours (EQ cyan, Space azure, MB purple, EQP gold, Tape amber, Tube copper, Glue red), staged auto-update + state migration, ScioEQ slopes + resonance sensitivity, ScioEQP drag fix, behavioral/preset analytics. User runs the phases in Claude Code.
- ScioMB: finish U6 + run U-phase tests, then build v2 from `ScioMB_v2_CodePrompts.md`
- Parameter tuning pass across all plugins: identify unusable zones, consider 15%-extreme-zone parametric mapping
- Telemetry redesign: session-based snapshots, delta check, retention/purge (fix foundation before event logging)
- ScioEQ/ScioEQP: parameter tuning + integration testing; resolve EQP 12AU7 / LF-freq reconciliations
- Test all plugins and log issues

## Blockers
<!-- Leave blank if none -->



































## Recent Commits
- 2026-06-17 · e0c5380 · feat(shared): global ScioSound settings store + shared settings panel
- 2026-06-17 · 4bb81a6 · chore(suite): branch next-beta, bump all plugins to 0.2.0 / β3
- 2026-06-17 · 6dc047e · docs(next-beta): unified beta spec + telemetry spec + autonomous build runner
- 2026-06-17 · 6047db0 · chore: restore AU format on all plugins (VST3 + AU)
- 2026-06-16 · 31e86c5 · fix(compressor): presets no longer pin oversampling (user-owned global)
