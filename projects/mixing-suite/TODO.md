# Mixing Suite — Todo Inbox

<!-- Quick-capture items noticed during chats. Newest items go at the bottom of "Open". -->
<!-- Format: - [ ] YYYY-MM-DD — item text -->

## Open
- [ ] 2026-06-17 — [NEXT-BETA][PRE-SHIP] In-DAW visual check of the metal knob + per-plugin whole-metal colours (only confirmed against an SVG mockup so far).
- [ ] 2026-06-17 — [NEXT-BETA][PRE-SHIP][BACKEND] Create the Supabase `feedback` table + 90-day retention job (sendEvent posts to .../rest/v1/feedback which must exist), and rotate the legacy anon key before wider release.
- [ ] 2026-06-17 — [NEXT-BETA][PRE-SHIP] Code-sign + notarize the installer so the opt-in staged auto-update can actually install; then smoke-test the download/"update ready" path.
- [ ] 2026-06-17 — [NEXT-BETA][PRE-SHIP] auval AU validation of all 7; force `-DCMAKE_BUILD_TYPE=Release` on MB/Tape/Tube build dirs (their CMAKE_BUILD_TYPE was empty).
- [ ] 2026-06-17 — [NEXT-BETA][FOLLOW-UP] Emit the 2 remaining preset events: preset_tweaked_after_load + preset_abandoned (preset_loaded + user_preset_saved already done). Additive in ScionaughBehavior + a per-processor "preset active/tweaked" flag.
- [ ] 2026-06-17 — [NEXT-BETA][PRODUCT CALL] Decide whether the §5 metal KNOB form should extend to EQ/EQP/MB (they currently keep size-appropriate compact arc-knobs but already take the per-plugin colour), or keep the split.
- [ ] 2026-06-17 — [NEXT-BETA] When pre-ship items clear: merge `next-beta` → develop and cut the β3 installer (scripts/build_mac_installer.sh; Inno Setup on Windows).

## Done
- [x] 2026-06-17 — [NEXT-BETA] Full autonomous runner COMPLETE — all 11 phases + final audit, all 7 plugins build green, committed on branch `next-beta` (4bb81a6 … f62dbfb). Verdict SHIP-READY (docs/prompts/next_beta_audit_report.md). Covered: version β3; shared settings store/panel; consent unification; in-plugin feedback; signal/stability probe; isNonRealtime playback/export oversampling (no audio-thread initProcessing); undo/redo; metal knob + faceplate; per-plugin whole-metal colours (all 7 locked heroes); EQ slopes + resonance sensitivity; EQP vertical-drag fix; opt-in staged auto-update + state-version; behavioural + preset analytics. New shared modules: ScionaughSettings.h, ScionaughFeedback.h, ScionaughSignalProbe.h, ScionaughUndo.h, ScionaughBehavior.h. (Remaining pre-ship items moved to Open above.)
