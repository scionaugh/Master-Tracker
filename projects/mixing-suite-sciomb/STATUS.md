# ScioMB — Multiband Dynamics + Colour

**Last Updated:** 2026-06-15
**Status:** ACTIVE — DSP (P0-P8) + UI (U0-U5) committed; v2 spec written, build next
**Priority:** MEDIUM

## Current Focus
DSP build complete (P0-P8, gated on T1-T8) and v1 UI implemented: `createEditor()` returns the real `ScionaughMBAudioProcessorEditor` (spectrum FIFOs, per-band GR atomics, SC tap, window shell, SpectrumPanel, MBBandStrip, MBRackRow, MBChainArrowButton, MBFocusedEditor with DEEP tab) — U0-U5 substantially done. DSP P0-P8 + UI U0-U5 committed to git on `develop` (cc69c22). Purple palette, shared ScionaughLookAndFeel. First suite plugin to compose existing engines per-band behind one band-splitting front end.

A **v2 upgrade spec is now agreed and written** (notes + phased prompts), but UNCOMMITTED and the code lags the notes. v2 = full Pro-MB 4-way dynamics (Compress/Expand + signed Range) on every topology, a 3-slot drag-to-reorder saturation stack of full §6.8 engines (~403 params total), and threshold-on-the-gain-bar (scroll the band block). ScioMB is the first suite plugin to intentionally break the strict physical-modeling rule (documented as a scoped exception in the principles doc §2.1).

## Next Actions
- Finish/verify U6 (final UI polish) + run the U-phase acceptance tests
- Build v2 via `ScioMB_v2_CodePrompts.md` (V1 dynamics computer → V2 dyn UI → V3 sat params+DSP → V4 sat UI → V5 threshold-bar → V6 presets/telemetry/gate; T9-T11)
- Deep-panel parity check vs ScioEQ/ScioEQP (UI §11 Q5 — resolved as a labelled DEEP tab)
- [CALIBRATE] tuning pass: diode ideality n / colour depth, transformer cores per voicing (Neve/API/SSL), API 2503/2622 ratios, SSL Jensen data, interpolated Aliveness ceilings
- Integration testing in a host

## Blockers
<!-- Leave blank if none -->

## Recent Commits
- 2026-06-14 · cc69c22 · ScionaughMB: DSP P0-P8 + UI U0-U5; EQ/EQP added to principles doc

## Notes
- v2 notes + `ScioMB_v2_CodePrompts.md` are uncommitted; current Source/ still reflects v1 (single sat slot, voicing in DEEP, no comp_mode/comp_range). Build v2 to bring code in line with notes.
