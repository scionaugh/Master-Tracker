# Mixing Suite

**Last Updated:** 2026-06-17
**Status:** ACTIVE — next-beta (v0.2.0/β3) unified build COMPLETE on branch `next-beta` (all 11 runner phases + audit, SHIP-READY); pending pre-ship manual items + 2 minor follow-ups

## Current Focus
Suite spans eight plugins (ScioSee added as the eighth). The four original plugins (ScioTube, ScioTape, ScioGlue, ScioSpace) are at beta v2 with stochastic noise complete. Both EQs (ScionaughEQ, ScionaughEQP) have prototype + UI v2 complete (2026-06-12). ScionaughMB (multiband) now has DSP (P0-P8) and UI v1 (U0-U5) committed to git (cc69c22); a v2 upgrade spec (4-way dynamics, 3-slot sat stack, threshold-on-bar) is written but uncommitted, with code still lagging the notes. NEW (2026-06-17): ScioSee (code target ScionaughAnalyser), a unified spectrum/scope/stereo analyzer with a pinned loudness meter, is fully spec'd with phased code prompts written to `Plugins/ScionaughAnalyser/`; it introduces a shared analysis+visualization layer in `Shared/` so display upgrades propagate suite-wide. Design done, no code yet.

**DONE 2026-06-17 — next-beta (0.2.0/β3) autonomous build:** all 11 runner phases + final audit complete and committed on branch `next-beta`, all 7 plugins build green. Delivered: shared global settings store + gear panel; consent unification (no more hard-coded setConsentGranted(true) — reads dataCollectionConsent); in-plugin feedback modal; signal/stability probe (in/out meters, NaN/Inf, CPU, clean-exit, in+out spectral scalars); playback/export oversampling via isNonRealtime (removed audio-thread initProcessing); per-instance undo/redo; metal knob + faceplate (cached body) + per-plugin whole-metal colour ramps (all 7 heroes match the locked table); ScioEQ HPF/LPF slope cascade + resonance sensitivity; ScioEQP vertical-drag fix; opt-in staged auto-update + state-version; behavioural + preset events. New shared header-only modules: ScionaughSettings.h, ScionaughFeedback.h, ScionaughSignalProbe.h, ScionaughUndo.h, ScionaughBehavior.h. Audit verdict SHIP-READY (report: `docs/prompts/next_beta_audit_report.md`); tests 16/17 (the 1 fail is a pre-existing stale codename-slug test, not a regression). Suite-level next focus: parameter tuning pass and the next-beta pre-ship items below.

## Next Actions
- NEXT-BETA PRE-SHIP (branch `next-beta`, build done): (1) in-DAW visual check of the metal knob + per-plugin colours — only confirmed against an SVG mockup so far; (2) create the Supabase `feedback` table + 90-day retention (sendEvent posts to a /feedback endpoint that must exist) and rotate the anon key; (3) code-sign + notarize so the opt-in staged auto-update can actually install; (4) auval AU validation; (5) force `-DCMAKE_BUILD_TYPE=Release` on MB/Tape/Tube build dirs; (6) optional: emit the 2 remaining preset events (preset_tweaked_after_load, preset_abandoned); (7) product call — extend the metal KNOB form to EQ/EQP/MB (currently compact arc-knobs; they already take the colour) or keep as-is. Then merge `next-beta` → develop and cut the β3 installer.
- ScioSee (ScionaughAnalyser): run the 7-phase extract-first build from `Plugins/ScionaughAnalyser/ScioSee_CodePrompts.md`, starting Phase 1 (SpectrumAnalyzer extraction + retrofit EQ/MB)
- ScioMB: finish U6 + run U-phase tests, then build v2 from `ScioMB_v2_CodePrompts.md`
- Parameter tuning pass across all plugins: identify unusable zones, consider 15%-extreme-zone parametric mapping
- Telemetry redesign: session-based snapshots, delta check, retention/purge (fix foundation before event logging)
- ScioEQ/ScioEQP: parameter tuning + integration testing; resolve EQP 12AU7 / LF-freq reconciliations
- Test all plugins and log issues

## Blockers
<!-- Leave blank if none -->












































## Recent Commits
- 2026-06-17 · be15c62 · fix(ui): dark-desaturated ScioGlue red + compact metal knob for EQ/EQP/MB
- 2026-06-17 · f62dbfb · chore(suite): next-beta final audit report
- 2026-06-17 · 495f755 · feat(update): opt-in staged auto-update + behavioral events + preset analytics
- 2026-06-17 · 2e2ab29 · feat(eq): HPF/LPF slope selector + resonance sensitivity; fix(eqp): vertical drag
- 2026-06-17 · 8ac59e3 · feat(ui): per-plugin whole-metal colour identities
