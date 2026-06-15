# ScioMB — Todo Inbox

<!-- Quick-capture items noticed during chats. Newest items go at the bottom of "Open". -->
<!-- Format: - [ ] YYYY-MM-DD — item text -->

## Open
- [ ] 2026-06-14 — Deep-panel parity check: confirm gear vs "..." vs DEEP tab against current ScioEQ/ScioEQP build (UI §11 Q5).
- [ ] 2026-06-14 — [CALIBRATE] diode-bridge: ideality n + kColourDepth to the 0.2%@+15dBu / 0.03%@nominal THD anchor.
- [ ] 2026-06-14 — [CALIBRATE] transformer cores per voicing; obtain API 2503/2622 ratios and SSL Jensen model data.
- [ ] 2026-06-15 — [PHASE] Add per-sample gain slew on the dynamic path (slew gain coeffs, not integrator states). Especially relevant here — bands move continuously by design. Free zipper reduction. (Phase notes §8.2)
- [ ] 2026-06-15 — [PHASE] Honestly relabel phase modes per chosen scheme (see ScioEQ). String change only. (Phase notes §6)
- [ ] 2026-06-15 — [PHASE][GATED] Optional "Natural Phase" (Vicanek matched biquad) — gated on audibility A/B AND an under-modulation check: confirm matched coeffs vary SMOOTHLY (no clicks) and per-block recompute stays cheap under constant gain modulation. (Phase notes §7 ScioMB, §9)
- [ ] 2026-06-15 — [PHASE] If band splits are min-phase crossovers, audit how near-Nyquist cramping interacts with the TOP crossover specifically. (Phase notes §7 ScioMB)
- [ ] 2026-06-15 — [PHASE][DO-NOT-BUILD] No cepstral FIR engine, no B→A hybrid switching — no overlap error to switch for. (Phase notes §8)
- [ ] 2026-06-15 — [NAMING] Apply suite naming convention across all three saturation categories: Tube→glow codenames, Tape→warm-tone codenames, Transformer→coil-family codenames; category words visible, voicing models hidden. Keep five topology labels plain (Optical/VCA/FET/Variable-Mu/Diode-bridge, slugs opt/vca/fet/vmu/db). Presets `Codename — Descriptor`. See docs/Mixing Suite - Naming Convention.md.

- [x] 2026-06-15 — [M1 priority] Idle CPU ~50% on drag into empty Ableton (no sat/no audio); gate OS path + instantiate saturators on demand (6x3 slots statically allocated). See ScioMB_Fixes_CodePrompts.md.
- [x] 2026-06-15 — [M2] Move AUTO MAKEUP off the orphaned terminal row onto the focused band's gain bar (UI notes §5.5/§11 Q4).
- [x] 2026-06-15 — [M3] Verify/add an explicit saturator remove ('x') affordance (v2 has set-None).

## Done
- [x] 2026-06-15 — Post-v2 fixes A (idle-CPU OS-gate, 14bb4dd) / B (auto-makeup chip on gain bar, 86c937c) / C ('×' sat-remove, eb4d688) DONE on develop.
- [x] 2026-06-14 — Stage 1/2/3 notes complete; DSP §10 ↔ UI §9 maps verified consistent.
- [x] 2026-06-14 — Phased code prompts P0-P8 written (ScioMB_CodePrompts.md).
- [x] 2026-06-14 — DSP implementation P0-P8 complete, all gates T1-T8 green.
