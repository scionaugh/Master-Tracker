# ScioSpace — Todo Inbox

<!-- Quick-capture items noticed during chats. Newest items go at the bottom of "Open". -->
<!-- Format: - [ ] YYYY-MM-DD — item text -->

## Open
- [ ] 2026-06-11 — Load the three prototype IRs into the JUCE project and verify playback through the convolution engine
- [ ] 2026-06-11 — Build juce::dsp::Convolution wrapper with M/S routing layer (owner: Gabriel, no deadline set); use non-uniform partitioned convolution for the Scoring Stage IR, zero-latency for Plate/Wood Room
- [ ] 2026-06-11 — Implement Mono-Safe / Expanded routing toggle with envelope-follower ducking on mid channel (Expanded mode only)
- [ ] 2026-06-11 — Implement loudness matching across all IRs so switching rooms doesn't cause level jumps
- [ ] 2026-06-11 — Implement hardcoded per-room pre-delay and ducking attack/release values (see chat-CONTEXT.md table)
- [ ] 2026-06-11 — Implement IR switch crossfade (5-10ms fade) on room dropdown change to avoid clicks
- [ ] 2026-06-11 — Source/synthesize additional IRs for full preset library (OpenAIR recommended starting point)
- [ ] 2026-06-11 — UI: add Room dropdown (Off / Plate / Wood Room / Scoring Stage) and Mono-Safe/Expanded toggle to top bar
- [ ] 2026-06-11 — UI: implement wireframe room box overlay on XY pad (log-scaled size, solid/dashed for mono-safe status, clipped at pad bounds, animated on room switch)
- [ ] 2026-06-11 — UI: update correlation meter to reflect live post-processing correlation (including reverb contribution)

## Done
- [x] 2026-06-10 — Implement a convolution room/reverb section. Targeted for beta v3. (superseded by detailed breakdown above, 2026-06-11)
- [x] 2026-06-11 — Generate three prototype IRs (plate mono-safe, wood room borderline, scoring stage wide) at 96kHz/24-bit
- [x] 2026-06-11 — Finalize reverb module DSP and UI design (routing, IR switching, mono-safe indicator, wireframe room, hardcoded ducking/pre-delay)
