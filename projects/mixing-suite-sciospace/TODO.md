# ScioSpace — Todo Inbox

<!-- Quick-capture items noticed during chats. Newest items go at the bottom of "Open". -->
<!-- Format: - [ ] YYYY-MM-DD — item text -->

## Open
- [ ] 2026-06-15 — [NAMING] Apply suite naming convention to user-facing preset/room labels (no real-gear names; `Codename — Descriptor` presets). See docs/Mixing Suite - Naming Convention.md.
- [ ] 2026-06-11 — Source/synthesize additional IRs for full preset library (OpenAIR recommended starting point)

## Done
- [x] 2026-06-15 — Convolution rooms (beta v3) IMPLEMENTED (Phase H, commits 4041680 + 1068075): M/S juce::dsp::Convolution, Mono-Safe/Expanded routing + ducking, per-room predelay/release, loudness-match + 8ms switch fade, room dropdown + toggle, animated wireframe overlay, live post-processing correlation. ConvolutionTests pass.
- [x] 2026-06-10 — Implement a convolution room/reverb section. Targeted for beta v3. (superseded by detailed breakdown above, 2026-06-11)
- [x] 2026-06-11 — Generate three prototype IRs (plate mono-safe, wood room borderline, scoring stage wide) at 96kHz/24-bit
- [x] 2026-06-11 — Finalize reverb module DSP and UI design (routing, IR switching, mono-safe indicator, wireframe room, hardcoded ducking/pre-delay)
