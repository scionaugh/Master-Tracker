# ScioEQP — Passive Program EQ (Pultec-style)

**Last Updated:** 2026-06-15
**Status:** ACTIVE — Prototype + UI v2 complete, parameter tuning next
**Priority:** MEDIUM

## Current Focus
First prototype complete and UI v2 complete (2026-06-12). Warm mahogany/brass palette. Passive program EQ with tube (12AU7) make-up and transformer colour via chowdsp_wdf (WBLT-warped reactive elements). UI v2 adds AbstractFifo spectrum analyser (input + output FFT), EQPAnalyserSettingsOverlay, legend (INPUT/OUTPUT only), updated display bg (#0c0600). No resonance detection, no M/S gradient. Next: parameter tuning and integration testing.

## Next Actions
- Parameter tuning pass (identify unusable zones, consider 15%-extreme-zone mapping)
- Integration testing in a host
- Resolve the two flagged param reconciliations (see Blockers / code-CONTEXT)
- [PHASE/future] Bake Vicanek matched-biquad coeffs as DEFAULT (analog identity); gate = band-type coverage check. + gain slew. See Research/dynamic_phase/Phase_Implementation_Notes.md

## Blockers
- 12AU7 param mismatch: ScionaughTube table has {21.5,1.30,1180,84,300} but EQP notes specify {17.0,1.35,4200,84,300} — use the EQP-notes values.
- ~~LF-freq mismatch~~ RESOLVED (Phase F, 632cfbc): APVTS is source of truth, 5 positions; 200 Hz cap = 165 nF PROVISIONAL (C∝1/f), dsp_notes §6 updated. Exact 200 Hz cap still a later LTSpice [CALIBRATE] item.

## Recent Commits
<!-- Auto-updated on each Mixing Suite git commit -->

## Completed (2026-06-15)
- LF selector reconciled to 5 functional positions (Phase F, 632cfbc); filter test PF pass (monotonic + stable). 12AU7 param reconcile still open (not in scope of Phase F).
