# Mixing Suite — Todo Inbox

<!-- Quick-capture items noticed during chats. Newest items go at the bottom of "Open". -->
<!-- Format: - [ ] YYYY-MM-DD — item text -->

## Open
- [ ] 2026-06-17 — [NEXT-BETA] Unified next-beta build, all 7 plugins, work in Plugins/Shared. Spec: docs/next_beta_unified_spec.md (telemetry detail: docs/telemetry_feedback_spec.md). Workstreams: (1) telemetry + in-plugin feedback w/ scalar signal snapshot; (2) shared settings page = global (consent/data, export OS, auto-update) in ScioSound config file + per-instance playback OS (2x default); (3) oversampling export-vs-playback via isNonRealtime, standardize EQP/Tube/Tape/MB/Glue; (4) opt-in background staged auto-update + state migration + signing; (5) metal visual pass (flat-top knurled knob, top-right light, no specular, cache body); (6) per-plugin WHOLE-METAL colours (each plugin already has an accent in code): ScioGlue->red, ScioSpace->light blue, ScioMB purple, rest keep; resolve EQ/Space blue clash + EQP/Tape/Tube amber cluster.
- [ ] 2026-06-17 — [NEXT-BETA][EQP] Control-consistency fix: high-boost uses left/right drag while low-freq uses up/down; make high-boost vertical (up/down) like the low. Check all EQP controls. (spec §6a)
- [ ] 2026-06-17 — [NEXT-BETA][EQ] HPF/LPF slope selector 12/24/36/48 dB/oct (cascade SVF stages; update SVFBand::response for display). (spec §6b)
- [ ] 2026-06-17 — [NEXT-BETA][EQ] Resonance-display sensitivity knob (0-100%) in Analyser Settings overlay; 0%=off, 100%=catch hat/kick transient resonances; map to prominence threshold + floor + confidence; use faster/peak-hold spectrum at high sens. (spec §6b)
- [ ] 2026-06-17 — [NEXT-BETA][UNDO] Per-instance undo/redo: add juce::UndoManager -> APVTS (all 7 built with nullptr now); beginNewTransaction() at existing beginChangeGesture sites; shared Undo/Redo buttons; audit non-APVTS state; scope to user gestures. (spec §6c)
- [ ] 2026-06-17 — [NEXT-BETA] Version 0.2.0 / β3 (kBetaNumber=3) all 7; branch next-beta off develop. Autonomous Opus build runner: docs/prompts/next_beta_runner.md. Spec+prompts committed; user runs phases in Claude Code.

## Done
