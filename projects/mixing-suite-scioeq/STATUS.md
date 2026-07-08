# ScioEQ — Parametric EQ (ZDF SVF)

**Last Updated:** 2026-06-22
**Status:** R11 COMPLETE — 4 features built + committed (1bc51aa, round/r11-scioeq)
**Priority:** MEDIUM

## Current Focus
Round 11 done. Four features on `round/r11-scioeq` (commit 1bc51aa), all default-off → bit-identical to pre-R11, auval PASS, SVFBandTests green, scope strictly `Plugins/ScionaughEQ/`:
- **Phase 1 — Transient/Sustain keying:** per-band `dyn_target` (Full/Transient/Sustain); consumes shared `TransientSustainDetector.h` via `setDetectionLevelOverride()` before `updatePerSample()`; detection site = band's first-stage bandpass (mirrors ScioMB F5).
- **Phase 2 — Adaptive carve:** `detSource` extended to 3 choices; `setAdaptiveMode()` forces DynCompress + SC source so the band ducks where the keyed source has in-band energy.
- **Phase 3 — Air-band shelf drift:** 9th SVFBand (index 8 → 47 Hz drift rate, decorrelated from bands 0–7); `airBandOn/Gain/Freq`; HiShelf corner drifts with existing Aliveness g-drift; folded into all lane transfer calcs.
- **Phase 4 — Vicanek matched biquad:** per-instance `matchedVoicing` toggle; `computeMatchedCoeffs()` for all 7 band types (z=exp(s/fs)); main curve = matched, dashed `laneTransferDbSVF()` overlay shows the cramped SVF reference.

## Next Actions
- **R6 dependency:** Reservation filed for R6 to extract the Vicanek solver from `SVFBand.h` → `Shared/AnalogModels/MatchedBiquad.h` (ScioEQ = reference consumer; EQP/MB want it too). Currently the solver lives in the plugin pending that extraction.
- Merge `round/r11-scioeq` → develop at the integration gate (after R6 globals land); beta badge via R6 at merge.
- Settle the matched-voicing user-facing label after the phase-notes §7 audibility A/B.
- Host verification pass (Ableton restart): carve preview, detector traces, air-band wander, matched-vs-SVF curves.

## Blockers
<!-- Leave blank if none -->
- Matched-biquad solver still plugin-local until R6 extracts it to Shared/ (Reservation pending-R6).

## Recent Commits
- 1bc51aa (round/r11-scioeq) — R11 four features.

## Completed (2026-06-22)
- R11 ScioEQ: transient/sustain keying, adaptive carve, air-band shelf drift, Vicanek matched-biquad voicing. Spec `docs/scioeq_round11_spec.md`; build prompt `docs/prompts/v0_3_round11_scioeq_build_prompt.md`.

## Completed (2026-06-20)
- R3: external sidechain bus + cross-instance/SC overlay (unified picker) + band-matched external-SC detection (`band{N}_detSource`). β7, on develop.

## Completed (2026-06-15)
- Factory presets authored (Phase E, 998266c): 9 presets, slug-safe; ScionaughEQPresetTests pass.
