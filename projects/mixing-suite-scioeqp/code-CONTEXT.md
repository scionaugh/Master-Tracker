# ScioEQP — Code Context

<!-- Only read when asked for deeper evaluation or cross-project analysis -->

## Architecture Overview
JUCE plugin. Passive EQ network as a chowdsp_wdf tree (no R-type adaptors, termination resistors removed, WBLT warping R_C=T'/2C, R_L=2L/T'). 12AU7 Koren tube make-up stage. AbstractFifo FFT feed for the analyser. APVTS string-slug enums, log NormalisableRange.

## Key Technical Decisions
- chowdsp_wdf via FetchContent (Phase 0 of the prototype prompt added it; it was not previously in the repo).
- WBLT (warped bilinear) on reactive elements, recomputed per block on parameter change (O(1)).
- Transformer-flux pink-noise Aliveness convention (later reused by ScioMB's transformer slot).

## Dependencies on Other Projects
- Establishes the WDF/WBLT pattern ScioMB P6 (transformer saturation) reuses.
- Shares ScionaughTube's Koren engine and StochasticEngine.h.

## Known Issues / Technical Debt
- 12AU7 param table disagreement between ScionaughTube and EQP notes (use EQP-notes {17.0,1.35,4200,84,300}).
- LF-freq position count mismatch (DSP 4 vs UI 5); reconcile via APVTS.

## Open Questions
- Final 12AU7 values and LF position set after reconciliation.
