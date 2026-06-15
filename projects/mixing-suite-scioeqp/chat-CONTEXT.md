# ScioEQP — Chat Context

<!-- Only read when asked for deeper evaluation or cross-project analysis -->

## Goals and Purpose
Passive program-EQ (Pultec-style) for the Scionaugh Mixing Suite. Warm mahogany/brass character to contrast the cold ScioEQ. Pairs a passive LC EQ network with a 12AU7 tube make-up stage and transformer colour.

## Key Decisions Made
- WDF (chowdsp_wdf) for the passive network with WBLT frequency warping on reactive elements.
- Tube make-up modelled with the suite Koren 12AU7 model.
- UI v2 deliberately simpler than ScioEQ: INPUT/OUTPUT spectrum only, no resonance/M-S layers.

## Constraints and Dependencies
- chowdsp_wdf is the WDF dependency (added via FetchContent in the prototype Phase 0); ScioMB's transformer slot reuses this WDF/WBLT precedent.
- Shared ScionaughLookAndFeel; warm palette token swap.

## Open Questions
- 12AU7 parameter set reconciliation (use EQP-notes values).
- LF-frequency position count (4 vs 5) — APVTS as source of truth.
