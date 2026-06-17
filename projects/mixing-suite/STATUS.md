# Mixing Suite

**Last Updated:** 2026-06-17
**Status:** ACTIVE — next-beta (v0.2.0/β3) unified build specced + autonomous runner ready on branch `next-beta`

## Current Focus
Suite spans eight plugins (ScioSee added as the eighth). The four original plugins (ScioTube, ScioTape, ScioGlue, ScioSpace) are at beta v2 with stochastic noise complete. Both EQs (ScionaughEQ, ScionaughEQP) have prototype + UI v2 complete (2026-06-12). ScionaughMB (multiband) now has DSP (P0-P8) and UI v1 (U0-U5) committed to git (cc69c22); a v2 upgrade spec (4-way dynamics, 3-slot sat stack, threshold-on-bar) is written but uncommitted, with code still lagging the notes. NEW (2026-06-17): ScioSee (code target ScionaughAnalyser), a unified spectrum/scope/stereo analyzer with a pinned loudness meter, is fully spec'd with phased code prompts written to `Plugins/ScionaughAnalyser/`; it introduces a shared analysis+visualization layer in `Shared/` so display upgrades propagate suite-wide. Design done, no code yet. Suite-level next focus: parameter tuning pass and telemetry redesign.

## Next Actions
- NEXT-BETA (v0.2.0 / β3): unified suite build specced (`docs/next_beta_unified_spec.md`) with an autonomous Opus runner (`docs/prompts/next_beta_runner.md`) on branch `next-beta`. Scope: shared settings page, telemetry + in-plugin feedback + signal scalars + stability, playback/export oversampling, undo/redo, metal visual pass + per-plugin whole-metal colours (EQ cyan, Space azure, MB purple, EQP gold, Tape amber, Tube copper, Glue red), staged auto-update + state migration, ScioEQ slopes + resonance sensitivity, ScioEQP drag fix, behavioral/preset analytics. User runs the phases in Claude Code.
- ScioSee (ScionaughAnalyser): run the 7-phase extract-first build from `Plugins/ScionaughAnalyser/ScioSee_CodePrompts.md`, starting Phase 1 (SpectrumAnalyzer extraction + retrofit EQ/MB)
- ScioMB: finish U6 + run U-phase tests, then build v2 from `ScioMB_v2_CodePrompts.md`
- Parameter tuning pass across all plugins: identify unusable zones, consider 15%-extreme-zone parametric mapping
- Telemetry redesign: session-based snapshots, delta check, retention/purge (fix foundation before event logging)
- ScioEQ/ScioEQP: parameter tuning + integration testing; resolve EQP 12AU7 / LF-freq reconciliations
- Test all plugins and log issues

## Blockers
<!-- Leave blank if none -->










































## Recent Commits
- 2026-06-17 · 495f755 · feat(update): opt-in staged auto-update + behavioral events + preset analytics
- 2026-06-17 · 2e2ab29 · feat(eq): HPF/LPF slope selector + resonance sensitivity; fix(eqp): vertical drag
- 2026-06-17 · 8ac59e3 · feat(ui): per-plugin whole-metal colour identities
- 2026-06-17 · 67f705f · feat(ui): metal knob + faceplate pass in shared look-and-feel
- 2026-06-17 · aafcd89 · feat(ui): per-instance undo/redo across the suite
