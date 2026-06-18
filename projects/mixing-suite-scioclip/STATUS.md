# ScioClip — Peak Control (Clipper / Limiter)

**Last Updated:** 2026-06-18
**Status:** ACTIVE — full design package COMPLETE in Plugins/ScionaughClip/ (DSP_Notes + Design_Scope + UI_Notes + ScioClip_CodePrompts + CMake scaffold) on next-beta; ready to build. No DSP code yet.
**Priority:** MEDIUM

## Current Focus
New suite peak processor (the 8th audio plugin / 9th counting ScioSee). One face, six v1 modes behind a ScioGlue-style Mode selector: Hard Clip, Soft Clip, True-Peak Limit, Clip→Limit, Fairchild 670 vari-mu, Neve 2254 diode-pair. Clean digital lane uses antiderivative antialiasing (ADAA) on the exact clip function + a true-peak brickwall limiter reusing ScioSee's shared `LoudnessMeter`; the two analogue modes are WDF circuit models (chowdsp_wdf). Stage-2 research doc is consolidated and marks all open questions resolved. Next step is to assess research sufficiency and write `DSP_Notes.md`.

## Decisions
- **2026-06-18 — Identity colour = verdigris / emerald green.** Only empty hue in the suite ramp (ScioGlue owns red/oxblood; Tube/Tape/EQP warm; EQ cyan / Space blue / MB violet). Fits the metal theme as oxidised-brass patina. Deepened to match the suite's faceplate depth (hero 0xff279069 ~ ScioSpace/ScioGlue depth). Hero ramp `setMetalPalette({0xff05140d,0xff0a2419,0xff124230,0xff1d6848,0xff279069,0xff5cb892,0xffb8e8d2})`.
- **2026-06-18 — Fairchild mode = WDF 6386 (DAFx-12), not ScioGlue's algorithmic vari-mu.** Mastering is the gentle low-GR regime where circuit-emergent behaviour is most audible; fits the §2.1 physical-model philosophy; CPU cost accepted (few instances). ScioGlue's TC timings + ratio curve are the voicing/sanity reference only.

## Next Actions
- Next: run the build from ScioClip_CodePrompts.md, starting Phase 1 (shared true-peak coeff extraction), then Phase 2 (scaffold). Opus 4.8 throughout, per-phase verify + final subagent audit.
- Confirm two residual lookups before/while writing: (1) factor shared true-peak coeffs out of `LoudnessMeter::buildTruePeakCoeffs()` (ScioSee owner), (2) verify Neve 2254 fixed-attack value (~5 ms) against the manual
- Pick the accent colour (research suggests red/crimson = "ceiling/danger"); confirm code target name + PLUGIN_CODE
- After DSP_Notes: Design_Scope.md, UI_Notes.md, phased CodePrompts (extract-first, mirror ScioSee build pattern)

## Blockers
<!-- Leave blank if none -->

## Recent Commits
<!-- Auto-updated on each Mixing Suite git commit -->

## Completed (2026-06-18)
- Stage-2 DSP research consolidated into a single build-ready doc (modes, ADAA math + antiderivatives, true-peak limiter spec, Fairchild 670 WDF values + 6386 tube equation, Neve 2254 schematic extraction, detail foldback, Aliveness sites, visualisation, JUCE gotchas, source refs, resolved decisions). Archived in code-CONTEXT references.
- Tracker project folder created.
- ScioClip_CodePrompts.md (8 phases + final audit, mirrors ScioSee prompt conventions): P1 shared true-peak coeff extraction, P2 scaffold+APVTS+mode dispatch, P3 clip+ADAA, P4 limiter+Clip-to-Limit, P5 foldback+brickwall, P6 Fairchild WDF, P7 Neve 2254 WDF, P8 UI Layout B. Build-order (not extract-first; shared layer already exists).
- Source-mining pass (2026-06-18) against the Drive primary-source library: DAFx-12 -> corrected 6386 equation grouping, full WDF values, Table 3 time-constant RC network + sidechain static-nonlinearity equations, 8x-OS note, odd-harmonic character; Paiva 2011 -> foldback corners confirmed verbatim (Fender ~100 Hz / Hammond ~30 Hz) + GC transformer model; Fairchild Manual = image-only scan (ms figures derive from RC network). DSP_Notes + Design_Scope updated.
- UI_Notes.md (9 sections, Layout B: pinned LoudnessDisplay + hero ScopeDisplay with delta/GR overlays + transfer-curve inset; reuses the ScioSee layer, only the inset + 2 overlay configs are new).
- Decisions: D4 default = clean mastering clipper; D7 layout = B (ScioSee-style pinned meter).
- Design_Scope.md (10 sections: six-mode table, shared scaffold + per-mode param tables, dependency map, brickwall composition, references, verdigris theming, UI direction, build, open decisions D1-D6).
- DSP_Notes.md (14 sections, mirrors ScioSee format) + CMakeLists.txt scaffold written to Plugins/ScionaughClip/. Fairchild section built around the WDF 6386; CMake mirrors ScioSee (PLUGIN_CODE ScCl, PRODUCT_NAME ScioClip, chowdsp_wdf fetched). Verified all referenced Shared/*.cpp exist.
