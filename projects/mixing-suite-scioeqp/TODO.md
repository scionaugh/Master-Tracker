# ScioEQP — Todo Inbox

<!-- Quick-capture items noticed during chats. Newest items go at the bottom of "Open". -->
<!-- Format: - [ ] YYYY-MM-DD — item text -->

## Open
- [ ] 2026-06-15 — [TRANSFORMER] Add a transformer-voicing SELECTOR (EQP already has transformer colour via chowdsp_wdf — expose voicing choice, mirror ScioMB's Transformer category). Voicings shown with coil-family codenames (Coil/Winding/Core/Spool/Tap), category label "Transformer" visible; internal slugs stay append-safe. See docs/Mixing Suite - Naming Convention.md §Transformer + ScioMB.
- [ ] 2026-06-15 — [NAMING] Codename the existing/added transformer voicings (coil family) + `Codename — Descriptor` presets. See docs/Mixing Suite - Naming Convention.md.
- [ ] 2026-06-14 — Reconcile 12AU7 params: use EQP-notes {17.0,1.35,4200,84,300}, not the ScionaughTube table values.
- [ ] 2026-06-15 — [PHASE] Bake Vicanek matched-biquad coeffs as the DEFAULT (analog-voiced EQ = matching the analog prototype IS its identity). No separate Natural Phase mode. (Phase notes §7 ScioEQP)
- [ ] 2026-06-15 — [PHASE][GATE] Band-type coverage check: confirm a clean Vicanek matched solution exists for EVERY band type ScioEQP offers — no bilinear fallback once matched is the only path. Classic bells/shelves fine; audit anything unusual. (Phase notes §9)
- [ ] 2026-06-15 — [PHASE] Add per-sample gain slew on dynamic path (slew gain coeffs, not integrator states). Free improvement. (Phase notes §8.2)
- [ ] 2026-06-15 — [PHASE] Pull matched-biquad from shared suite code (build once, validated float32 at extremes). Do not reimplement per plugin. (Phase notes §8.3)

## Done
- [x] 2026-06-15 — LF-freq reconciled to 5 positions (Phase F, 632cfbc); 200 Hz cap = 165 nF PROVISIONAL (C∝1/f); dsp_notes §6 updated. Exact cap still a later LTSpice calibration item.
