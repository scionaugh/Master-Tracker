# ScioMB — Multiband Dynamics + Colour

**Last Updated:** 2026-06-15
**Status:** ACTIVE — v1 + v2 built and committed; post-v2 fixes are the current front
**Priority:** MEDIUM

## Current Focus
v1 (DSP P0-P8 + UI U0-U6) and **v2 are both built and committed** to git on `develop`. v2 = `6b037c6` ("ScionaughMB v2 (V1–V6): 4-way dynamics, 3-slot sat stack, threshold-on-bar", 18 files, +2945). Shipped: full Pro-MB 4-way dynamics (Compress/Expand + signed Range) on every topology; a 3-slot drag-to-reorder saturation stack of full §6.8 engines (satK_ prefix); model/voicing on the saturation FACE; threshold-on-the-gain-bar (scroll the band block) with band-lane visual feedback; DynModeTests; presets. Code no longer lags the notes. ScioMB is the first suite plugin to intentionally break the strict physical-modeling rule (principles §2.1, now generalised by the 2026-06-15 amendment: parameter-freeing + §2.1a hybrid honesty + §2.1b measured/sampled/synth behaviour).

The work front is now **post-v2 fixes**, specced in `Plugins/ScionaughMB/ScioMB_Fixes_CodePrompts.md` (M1–M3) and `TODO.md`.

## Next Actions
- **[M1, priority] Idle-CPU fix.** Dropping ScioMB on a track in an empty Ableton project spikes CPU ~50% with no saturation and no audio. Suspects: (a) the oversampling/crossover/FIR path runs every block even when nothing nonlinear is active; (b) all 6×3 saturator slots + 6 transformers are statically allocated/prepared at OS rate, so saturators may run when set to None. Fix: instantiate/run a saturator only when actually placed on a band; bypass the OS round-trip when no nonlinearity is active. Diagnose under Opus, fix under Sonnet.
- **[M2] Auto-makeup onto the gain bar.** Committed editor still has the orphaned terminal row holding only AUTO MAKEUP (band gain already moved to the spectrum bar). Move it to an AUTO chip on the focused band's gain bar + "+x.x dB auto" readout; remove the terminal row. Spec: UI notes §5.5 / §11 Q4.
- **[M3] Sat remove control** — verify whether v2's set-None gives an explicit remove affordance; if not, add an "x" (Haiku-cheap).
- [CALIBRATE] tuning pass: diode ideality n / colour depth, transformer cores per voicing (Neve/API/SSL), API 2503/2622 ratios, SSL Jensen data, interpolated Aliveness ceilings
- Integration testing in a host

## Blockers
<!-- Leave blank if none -->

## Recent Commits
- 2026-06-15 · 6b037c6 · ScionaughMB v2 (V1–V6): 4-way dynamics, 3-slot sat stack, threshold-on-bar
- 2026-06-14 · cc69c22 · ScionaughMB: DSP P0-P8 + UI U0-U5; EQ/EQP added to principles doc

## Notes
- v2 fully committed in 6b037c6 (supersedes the earlier "uncommitted, code lags notes" status).
- Post-v2 fixes captured in `ScioMB_Fixes_CodePrompts.md` (M1 CPU, M2 auto-makeup-on-bar, M3 sat-remove). Run M1 before any further feature work.

## Queued Fix (2026-06-15)
- Post-v2 fixes A/B/C now live in the unified \`docs/Suite_Fixes_CodePrompts.md\` (Phase A idle-CPU, B auto-makeup-on-bar, C sat-remove); it supersedes the MB-only ScioMB_Fixes_CodePrompts.md for execution.
