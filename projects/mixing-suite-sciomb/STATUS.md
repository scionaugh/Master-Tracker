# ScioMB — Multiband Dynamics + Colour

**Last Updated:** 2026-06-15
**Status:** ACTIVE — v1 + v2 + post-v2 fixes all built and committed; calibration + host tuning next
**Priority:** MEDIUM

## Current Focus
v1 (DSP P0-P8 + UI U0-U6) and **v2 are both built and committed** to git on `develop`. v2 = `6b037c6` ("ScionaughMB v2 (V1–V6): 4-way dynamics, 3-slot sat stack, threshold-on-bar", 18 files, +2945). Shipped: full Pro-MB 4-way dynamics (Compress/Expand + signed Range) on every topology; a 3-slot drag-to-reorder saturation stack of full §6.8 engines (satK_ prefix); model/voicing on the saturation FACE; threshold-on-the-gain-bar (scroll the band block) with band-lane visual feedback; DynModeTests; presets. Code no longer lags the notes. ScioMB is the first suite plugin to intentionally break the strict physical-modeling rule (principles §2.1, now generalised by the 2026-06-15 amendment: parameter-freeing + §2.1a hybrid honesty + §2.1b measured/sampled/synth behaviour).

**Post-v2 fixes A/B/C are DONE and committed (2026-06-15)** via the unified `docs/Suite_Fixes_CodePrompts.md`. The work front is now calibration + host verification.

## Next Actions
- Confirm idle CPU % in Ableton (Phase A fix landed; live measurement pending — no host in the build env)
- [CALIBRATE] tuning pass: diode ideality n / colour depth, transformer cores per voicing (Neve/API/SSL), API 2503/2622 ratios, SSL Jensen data, interpolated Aliveness ceilings
- Integration testing in a host
- [PHASE/future] Gain slew on dynamic path (esp. relevant — bands move continuously) + mode relabel; optional "Natural Phase" matched biquad gated on A/B AND under-modulation smoothness/cost check; audit top-crossover cramping. See Research/dynamic_phase/Phase_Implementation_Notes.md

## Blockers
<!-- Leave blank if none -->

## Recent Commits
- 2026-06-15 · eb4d688 · ScioMB Phase C: explicit '×' remove glyph on saturator cards
- 2026-06-15 · 86c937c · ScioMB Phase B: AUTO make-up chip on the focused band's gain bar
- 2026-06-15 · 14bb4dd · ScioMB Phase A: gate OS round-trip on actual nonlinearity (idle-CPU fix)
- 2026-06-15 · 6b037c6 · ScionaughMB v2 (V1–V6): 4-way dynamics, 3-slot sat stack, threshold-on-bar
- 2026-06-14 · cc69c22 · ScionaughMB: DSP P0-P8 + UI U0-U5; EQ/EQP added to principles doc

## Notes
- v2 fully committed in 6b037c6 (supersedes the earlier "uncommitted, code lags notes" status).
- Post-v2 fixes DONE 2026-06-15 (unified `docs/Suite_Fixes_CodePrompts.md`): A idle-CPU (14bb4dd — root cause was unconditional 26-ch×4 oversampling, not idle saturators; OS-gate + constant-latency osCompDelay, OS path bit-identical), B auto-makeup chip on the gain bar (86c937c, terminal row removed), C '×' sat-remove glyph (eb4d688).

## Completed (2026-06-15)
- Post-v2 fixes A (idle-CPU gate), B (auto-makeup on gain bar), C ('×' sat-remove) all committed on develop. See Notes.
