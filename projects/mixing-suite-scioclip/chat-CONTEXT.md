# ScioClip — Chat Context

<!-- Only read when asked for deeper evaluation or cross-project analysis -->

## Goals and Purpose
A multi-mode peak processor for the Scionaugh / ScioSound Mixing Suite: a clean digital lane (clipper + true-peak brickwall limiter) plus two circuit-modelled analogue limiter modes (Fairchild 670 vari-mu, Neve 2254 diode-pair), all behind one face — the ScioGlue "many topologies, one face" pattern. Context-flexible: usable on track, bus, or master. Supersedes an earlier ScioClip draft.

## Key Decisions Made (all Stage-2 open questions resolved in the research doc)
- Honesty framing: clip modes are not a device, so they are anchored on the exact clip function made alias-free by antiderivative antialiasing (ADAA) — the clipper analogue of exact prewarped shelf coeffs in ScioEQ. True-peak limit = honest DSP control system. Analogue modes = published circuit models. Detail foldback = §2.1 physics (transformer LF saturation). Only the Optimize make-up is a §2.1a hybrid, and it is deferred to v2.
- v1 modes: Hard Clip, Soft Clip (cubic primary, tanh secondary), True-Peak Limit, Clip→Limit, Fairchild Vari-Mu, Neve 2254.
- ADAA: first-order ADAA inside a 2× oversampled path is the default in every mode (Off / 2× / 4× user options). Second-order held in reserve.
- True-peak detection reuses ScioSee's shared `LoudnessMeter` (BS.1770) — one calibration, two call sites (observational meter + predictive lookahead detector).
- Lookahead fixed at 1 ms (no user control in v1). Limiter release switchable: GR-proportional (default) vs peak-density-driven.
- Fairchild: all six program-dependent time-constant settings modelled in v1. Neve diode = generic Shockley pair fitted to the HBX31 shunt transfer, WDF so DAFx-20 ADAA applies.
- Brickwall safety toggle composes the digital limiter after the analogue stage. Optimize make-up deferred to v2. Spectral per-band clip mode parked for later.

## DSP_Notes readiness matrix (assessment 2026-06-18)
- Hard / Soft clip + ADAA: READY — exact functions and first/second antiderivatives are given.
- True-Peak Limit + Clip→Limit: READY — full lookahead gain-computer spec; depends on the shared LoudnessMeter (exists) and one factoring task.
- Neve 2254: READY — schematic extracted (neve2254_extracted.md), topology corrected, modelling boundary stated; one ~5 ms attack lookup outstanding.
- Fairchild 670: READY — 6386 tube equation + all 8 params, WDF transformer/resistor/cap values, sidechain, fidelity boundary all present.
- Detail foldback, Aliveness, visualisation, JUCE gotchas: READY — structure, sites and formulas specified, all reusing existing suite modules.
- Net: research is sufficient to write the full v1 DSP_Notes now. The two residual items are lookups, not blockers.

## Constraints and Dependencies
- Depends on ScioSee's shared layer (`Shared/Analysis/LoudnessMeter`, `Shared/Visualization/LoudnessDisplay`) and on `chowdsp_wdf` already fetched for ScioSee.
- Reuses ScioGlue (vari-mu engine, auto-release ballistics, GR scope, Delta overlay) and the shared StochasticEngine / HarmonicAnalyzer.
- Brand is ScioSound (product-facing); code symbols stay Scionaugh*.

## Open Questions
- Confirm code target name (ScionaughClip) and PLUGIN_CODE before scaffolding.
- Per-mode detail-foldback defaults (Fairchild iron vs Neve iron differ) — pin sensible voiced defaults.

## Codebase validation (2026-06-18) — decisions DSP_Notes must resolve
Research is confirmed sufficient and its reuse map is accurate, with two scope decisions now forced by what already exists:
1. **Fairchild fidelity: RESOLVED 2026-06-18 -> WDF 6386.** Build the §6 DAFx-12 WDF circuit model (6386 remote-cutoff triode equation + transformers + sidechain), NOT a reuse of ScioGlue's algorithmic vari-mu. Rationale: mastering is the gentle low-GR regime where emergent circuit behaviour (mu shift with bias, transformer weight + intermodulation) is most audible; fits the §2.1 physical-model philosophy; keeps the two vari-mus distinct (ScioGlue = fast mix-bus, ScioClip = circuit-accurate mastering). Reuse ScioGlue's validated TC timings + ratio curve as the voicing/sanity reference; parameter-freeing still recovers the compressor. CPU cost (Newton-Raphson per sample, C1 delay-free loop, convergence care near zero) accepted since mastering runs few instances.
2. **Neve 2254 is new DSP:** the existing DiodeBridge is the 33609 four-diode bridge, not the 2254 two-diode shunt — confirmed by §7.1. Model the shunt fresh.
Everything else validated: shared LoudnessMeter exists and names the clipper as a consumer; chowdsp_wdf is fetched; param-prefix house convention is `opt_/vca_/fet_/vm_/db_` (vari-mu = `vm_`); DSP_Notes should follow the ScioSee section format (threading contract first).
