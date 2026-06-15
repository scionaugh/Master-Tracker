# ScioEQP — Passive Program EQ (Pultec-style)

**Last Updated:** 2026-06-14
**Status:** ACTIVE — Prototype + UI v2 complete, parameter tuning next
**Priority:** MEDIUM

## Current Focus
First prototype complete and UI v2 complete (2026-06-12). Warm mahogany/brass palette. Passive program EQ with tube (12AU7) make-up and transformer colour via chowdsp_wdf (WBLT-warped reactive elements). UI v2 adds AbstractFifo spectrum analyser (input + output FFT), EQPAnalyserSettingsOverlay, legend (INPUT/OUTPUT only), updated display bg (#0c0600). No resonance detection, no M/S gradient. Next: parameter tuning and integration testing.

## Next Actions
- Parameter tuning pass (identify unusable zones, consider 15%-extreme-zone mapping)
- Integration testing in a host
- Resolve the two flagged param reconciliations (see Blockers / code-CONTEXT)

## Blockers
- 12AU7 param mismatch: ScionaughTube table has {21.5,1.30,1180,84,300} but EQP notes specify {17.0,1.35,4200,84,300} — use the EQP-notes values.
- LF-freq mismatch: EQP DSP notes list 4 positions (20/30/60/100 Hz), UI notes' LOW selector shows 5 (adds 200 Hz) — reconcile with APVTS as source of truth.

## Recent Commits
<!-- Auto-updated on each Mixing Suite git commit -->
