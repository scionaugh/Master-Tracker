# ScioClip — Code Context

<!-- Only read when asked for deeper evaluation or cross-project analysis -->

## Architecture Overview
New JUCE VST3/AU plugin target (proposed `ScionaughClip`, product "ScioClip"). One plugin, all modes, following ScioGlue exactly: a `Mode` selector with per-mode prefixed parameter IDs so inactive modes retain values (§4.3); enums attach by slug via `ComboBoxAttachment`, never by index. Each mode is a small engine class behind a common `processBlock` dispatch (the ScioMB micro-rack pattern).

Mode list (v1): Hard Clip · Soft Clip · True-Peak Limit · Clip→Limit · Fairchild Vari-Mu · Neve 2254.

## Key Technical Decisions
- **Clip core:** exact piecewise clip functions (hard; cubic soft primary, tanh optional) rendered alias-free with first-order ADAA + 2× oversampling (Off/2×/4×). ADAA state (x[n-1], F1(x[n-1])) is per-channel filter state; reset in prepareToPlay, preserved across blocks.
- **True-peak limiter:** 1 ms fixed lookahead gain computer on the 4× oversampled true-peak signal; reuses the shared `LoudnessMeter` polyphase true-peak FIR (one calibration, two call sites). Release switchable: ScioGlue GR-proportional (default) vs peak-density. Default ceiling −1.0 dBTP.
- **Fairchild Vari-Mu:** WDF (chowdsp_wdf) of the Class-A 6386 remote-cutoff vari-mu stage; tube via Newton-Raphson on the implicit equations. All six program-dependent time constants modelled. Source: Raffensperger DAFx-12.
- **Neve 2254:** two-diode shunt variable attenuator (HBX31 pair, board B185) — NOT a bridge (that's the 33609). Generic Shockley diode pair fitted to the shunt transfer; WDF so DAFx-20 ADAA applies. Source: Neve schematic L/10,004 + neve2254_extracted.md.
- **Brickwall toggle** (analogue modes): composes the §5 digital limiter after the analogue stage to guarantee the ceiling.
- **Detail foldback:** out = clip(x) + foldback · HPF_minphase(x, fc); minimum-phase FIR; physically grounded in transformer LF saturation (Paiva et al. 2011).
- **Aliveness (Stochastic Injection):** clip modes → threshold micro-jitter on ceiling L scaled by overshoot; limit modes → gain-coefficient jitter scaled by |GR_dB|; Fairchild/Neve inherit ScioGlue per-topology sites. Never additive output noise.
- **DC blocking** after asymmetric clip; output normalisation so default drive ≈ unity.
- Param-ID prefixes: clip_*, lim_*, vm_*, neve_*, plus shared in_drive, ceiling, mix, os, alive, fold_amt, fold_fc, lim_release_mode.

## Dependencies on Other Projects
- ScioSee shared layer: `Shared/Analysis/LoudnessMeter`, `Shared/Visualization/LoudnessDisplay`, plus `chowdsp_wdf` (already fetched in ScioSee CMake).
- ScioGlue: vari-mu engine, auto-release ballistics, GR scope, Delta Ghost Waveform overlay.
- Shared: StochasticEngine.h, HarmonicAnalyzer, ScionaughLookAndFeel/Colours/MetalPalette.
- Build: CMake target following the suite pattern (PRODUCT_NAME "ScioClip", COMPANY_NAME "Scionaugh", MANUFACTURER_CODE Scio, PLUGIN_CODE ScCl proposed (consistent with ScioSee=ScSe), BUNDLE_ID com.scionaugh.clip, VST3 + AU).

## Known Issues / Technical Debt
- Codebase Plugins/ tree is not mounted in the tracker session; DSP_Notes.md / Design_Scope.md / UI_Notes.md should be authored into `Plugins/ScionaughClip/` in the repo (mirror ScionaughAnalyser layout).

## Research Source Files (in the ScioClip research folder)
- ScioClip_DSP_Research.md (consolidated Stage-2 doc — primary).
- dafx12_submission_9-fairchild.pdf (Raffensperger Fairchild 670 WDF).
- Neve-2254-Limiter-Schematic.pdf + neve2254_extracted.md.
- Paiva et al. 2011 transformer emulation (EURASIP, open access).
- ADAA refs (Parker/Bilbao/Esqueda/Holters/Albertini/Chowdhury), minimum-phase FIR refs (Damera-Venkata, Pei-Lin).

## Open Questions
- Confirm code target name + PLUGIN_CODE before scaffolding.
- Residual lookups: Neve fixed-attack (~5 ms); factor shared true-peak coeffs out of LoudnessMeter.

## Validation against codebase (next-beta, 2026-06-18)
Checked the research's reuse claims against the actual repo. All shared dependencies exist; two scope decisions surfaced that DSP_Notes must resolve.
- **LoudnessMeter** (`Shared/Analysis/LoudnessMeter.{h,cpp}`) exists: BS.1770-4, 4× polyphase true-peak (`kOsTaps=12`, `kOsFactor=4`), and its header comment explicitly names "the future Limiter/Clipper" as a consumer. `buildTruePeakCoeffs()` is still a private method — the "factor it into a shared header" task is real and not yet done. (Research cites BS.1770-5; code is -4; equivalent for TP/K-weighting, as the research already notes.)
- **chowdsp_wdf** is FetchContent-fetched in both ScioGlue and ScioSee CMake; ScioClip CMake follows the same block.
- **House param-prefix convention CONFIRMED** in ScioGlue: `opt_`/`vca_`/`fet_`/`vm_`/`db_`. Corrected ScioClip's vari-mu prefix to `vm_` (research wrote `vmu_`) to match the suite. Neve mode keeps a distinct prefix (`neve_`).
- **Fairchild — fidelity DECISION surfaced.** ScioGlue ALREADY ships a Fairchild 670 vari-mu engine: `processVariMu`, `variMuRatio` (~2:1→30:1), the full 6-position TC table (TC1–TC6, two Auto), and `sustainAccum` auto-release. But it is an algorithmic control-law model (ratio curve + asymmetric-tanh colour stage), NOT the WDF 6386 tube model the research §6 specifies. So §6 (new DAFx-12 WDF) and §13 ("reuse ScioGlue vari-mu engine") are in tension. DECISION (2026-06-18): build the WDF 6386 model (option b). Mastering is the low-GR regime where circuit-emergent behaviour matters most and CPU cost is affordable (few instances); reuse ScioGlue's TC timings + ratio curve only as the voicing/sanity reference. The vari-mu engine in DSP_Notes is therefore the DAFx-12 WDF, not the ScioGlue algorithmic model.
- **Neve 2254 ≠ the existing DiodeBridge.** ScioGlue's `DiodeBridge.h` is the four-diode bridge (33609-style, ported from ScioMB). The 2254 is a two-diode shunt attenuator (research §7.1 is explicit). DiodeBridge is a useful diode-WDF + ADAA reference pattern, but the 2254 shunt is genuinely new DSP — do not reuse DiodeBridge as the 2254.
- **Lookahead** is new: no `lookahead` anywhere in ScioGlue, confirming the 1 ms lookahead limiter is net-new to ScioClip.
- **DSP_Notes format to mirror** (`ScionaughAnalyser/DSP_Notes.md`): "## 0 Threading contract" first, then one numbered "## N" section per engine/module with its source path as subtitle, ending in a performance-notes section.


## Canonical research library (Google Drive, 2026-06-18)
The full primary-source set lives in a shared Drive folder (id 18sq_HXTTtyi4EZHt8ylcYtz2NabLoz8g): Fairchild Manual + dafx12 WDF paper + 670 schematics; Neve 2254 schematics (Neve-2254-Limiter-Schematic.pdf, Neve 2254:R schematic.pdf, 707098930-NEVE-SCHEMATICS.pdf, B185 board photo); Paiva transformer paper (art_10.1155_2011_347645); min-phase FIR papers (Hilbert + real-cepstrum); Jiles-Atherton set; Oppenheim & Schafer DTSP; Pirkle "Designing Audio Effect Plugins in C++"; Reiss/McPherson "Audio Effects".

Neve 2254 fixed-attack lookup RESOLVED (2026-06-18): the folder holds only schematics, no 2254 manual with a numeric attack spec. The schematic (L/10,004) confirms the B185 shunt element + low-noise buffers, BA340 output amp, B191/B192 buffers, BAX16 sidechain diodes, attack switch positions (100/200, plus 800 per extraction), AUTO recovery — but no ms attack figure. Therefore the ~5 ms "fixed attack" is a MODELLED value from the sidechain RC + transformer, not a datasheet spec; pin it from the circuit and stop chasing a spec that does not exist in the sources.


## Mined source data folded into DSP_Notes (2026-06-18)
- 6386 equation (DAFx-12 Eq.1, corrected): Ia = (p3 - p4*Vgk)^p5 * [p6 + p1*Vak^p2 * exp(p7*Vak - p8*Vgk)] — p1*Vak^p2 is INSIDE the bracket (earlier draft had it outside). Verify superscripts against the PDF.
- Time-constant circuit = DAFx-12 Table 3 RC network (CT/CU/CV/RT/RU/RV per 6-way switch; positions 5/6 engage RU/RV for program-dependent "Auto" release). These RC values are the model truth; ScioGlue TC labels are the UI cross-check.
- Sidechain static nonlinearity (DAFx-12 §5): phi'_DC=12.2(phi_DC+0.1); Vstage1 ln-ratio Class B; Vstage3=clamp(8.4*Vstage1,±100); bridge diode lambda=10/Vd=0.3/Rout~160; Isc saturation Imax=0.5A. Sidechain output xfmr Np/Ns=4, Np/Nt=9.5 (tertiary feedback). Black-boxed tubes: 12AX7 x2, 12BH7A x2, 6973.
- Fairchild OS: paper uses 8x (aliasing + bilinear-warp + fictitious-delay error); suite max 4x, so force OS>=2x for analogue modes, consider private internal higher OS.
- Fairchild harmonics: balanced topology = ODD only (H3 ~21 dB below fund at low GR, ~14 dB at heavy GR). Opposite of asym-clip even H2.
- Foldback corners (Paiva 2011 verbatim): LF distortion below ~100 Hz (Fender), ~30 Hz (Hammond). Physics anchor for analogue-mode foldback default; clip-mode user fc range stays higher (500 Hz-12 kHz) for creative snap/air. Paiva GC core (Ce=Cc/(1+a|vc|^(n-1))) is an alternative to ScioTape's J-A; use J-A.
- Fairchild Manual.pdf = manualslib image-only scan (ToC machine-readable only); per-position ms come from the RC network, not the manual.
