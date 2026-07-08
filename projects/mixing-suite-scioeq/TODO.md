# ScioEQ — Todo Inbox

<!-- Quick-capture items noticed during chats. Newest items go at the bottom of "Open". -->
<!-- Format: - [ ] YYYY-MM-DD — item text -->

## Open
- [ ] 2026-06-15 — [TRANSFORMER] Add a transformer-voicing selector to this module (mirror ScioMB's Transformer saturation category). Voicings shown with coil-family codenames (Coil/Winding/Core/Spool/Tap), category label "Transformer" visible; internal slugs stay append-safe. See docs/Mixing Suite - Naming Convention.md §Transformer + ScioMB.
- [ ] 2026-06-15 — [NAMING] Apply suite naming convention to any transformer voicings/presets added (coil-family codenames, `Codename — Descriptor` presets). See docs/Mixing Suite - Naming Convention.md.
- [ ] 2026-06-15 — [PHASE] Add per-sample gain slew on the dynamic path (slew mixing/gain coeffs, NOT integrator states). Free zipper reduction (-58→-74 dB); do regardless of matched-biquad decision. (Phase notes §8.2)
- [ ] 2026-06-15 — [PHASE] Honestly relabel existing ZDF SVF mode per chosen scheme — 2-mode (Minimum Phase / Linear Phase) or 3-mode (Zero Latency / Natural Phase / Linear Phase). String change, no DSP. Avoid naming Zero-Latency + Minimum-Phase as siblings (same filter). (Phase notes §6)
- [ ] 2026-06-15 — [PHASE][DO-NOT-BUILD] No cepstral FIR engine (Mechanism A): provably identical to existing min-phase mode for a cascade EQ; ~11-12× cost, buys nothing. Keep as offline reference only. (Phase notes §1-2, §8)

## Done
- [x] 2026-06-22 — [PHASE][GATED] "Natural Phase" = Vicanek matched biquad — BUILT in R11 Phase 4 (per-instance matchedVoicing toggle, computeMatchedCoeffs() all 7 band types, dashed SVF cramp overlay). Solver plugin-local pending R6 extraction to Shared/AnalogModels/MatchedBiquad.h (Reservation filed). Final user-facing label gated on phase-notes §7 A/B. (commit 1bc51aa)
- [x] 2026-06-22 — [R11] Transient/Sustain dynamic-band keying (dyn_target Full/Transient/Sustain) via shared TransientSustainDetector.h. (1bc51aa)
- [x] 2026-06-22 — [R11] Adaptive / unmask carve off the SignalBus overlay source (band-matched detection). (1bc51aa)
- [x] 2026-06-22 — [R11] Air-band wide top shelf with corner drift tied to existing Aliveness g-drift; static at zero. (1bc51aa)
