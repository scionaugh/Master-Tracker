# Sciotape — Chat Context

## Goals and Purpose
A physically grounded tape saturation plugin using the Jiles-Atherton hysteresis integrator as its nonlinear core — the direct structural equivalent of the Koren equations in Sciotube. Explicitly not a waveshaper or tanh approximation. The model carries state between samples.

## Key Decisions Made

**Core model: Jiles-Atherton differential equation**
Chosen over tanh approximation after explicit evaluation. Waveshaper approach was considered and rejected. The J-A model is not memoryless.

**Seven-parameter model table**
Each tape formulation defined by: MS (saturation magnetisation), Hc (coercivity), Mr (remanence ratio), a (domain wall density), k (pinning coefficient), alpha (inter-domain coupling), Bias (nominal operating point as multiple of Hc), Gap (head gap length in µm).

**Five starting library formulations**
- Ferric LH (early oxide)
- Ampex 456 (high-output ferric — primary calibration reference)
- Scotch 250 (studio ferric)
- Agfa PEM 468 (extended range)
- Metal Particle

All parameters are calibration starting points only. Require curve-fitting against Dempwolf et al. 2011 measured B-H data before production.

**Complete two-stage record/playback chain — six discrete stages in order:**
Record amplifier (Drive + pre-emphasis EQ) → upsample 8x → bias injection → J-A integrator → playback head differentiation (dM/dt) + gap loss sinc filter → downsample 8x → playback amplifier (post-EQ + output gain). Stages may not be merged or reordered.

**8x oversampling as architectural minimum**
AC bias oscillator runs at oversampled internal rate (352.8 kHz at 44.1 kHz host). Must be summed with signal before J-A integrator — not after. 4x acceptable for Phase 1 browser prototype only, with reduced bias frequency of 40–60 kHz.

**Bias as a real-time creative control**
Range: -50% to +100% of nominal operating point. Under-bias → even-harmonic dominance (H2 >> H3). Correct bias → minimises even-order distortion. Over-bias → progressive HF attenuation via self-erasing effect. Identified as the most musically powerful tape-specific control.

**Physics offset panel (secondary controls)**
Five parameters exposed as real-time offsets: Onset/Hc, Ceiling/MS, Character/Mr, Knee/a, Thickness/k. All at 0% restores exact loaded model.

**Head Gap as separate creative parameter**
Gap (0.5–10 µm) sets frequency of sinc rolloff null in real time. At 15 ips, 2.5 µm gap places null at 152 kHz; at 3.75 ips same gap brings null to ~19 kHz.

**B-H loop as primary display**
Hysteresis loop (M vertical, H_total horizontal) updated in real time from actual signal. Currently traversed region highlighted.

**Three-phase implementation path**
- Phase 1: Browser prototype (JS + Web Audio API, AudioWorklet, 4x OS)
- Phase 2: Reaktor Core (ZDF feedback loop, SR.R timestep, 8x OS)
- Phase 3: JUCE C++ (production — polyphase FIR OS, per-parameter smoothing, ring buffer for B-H display, IEC/NAB biquad EQ chain with speed-dependent coefficient table)

**Architecture document complete**
`Tape Saturator/Tape_Saturator_Design_Outline.md` (21 KB, v1.0) — full signal flow diagram, per-stage DSP spec including all equations, tape model library table, controls spec, visual feedback spec, implementation path for all three phases, parameter fitting procedure. This is the Claude Code handoff document.

## Critical Implementation Constraints
- **coth singularity must be guarded:** Man = MS * (coth(H/a) - a/H) undefined at H=0. Replace with Taylor expansion Man = MS * H/(3*a) for |H| < epsilon. Will produce NaN at signal zero-crossings if not implemented.
- **Denominator guard required:** (k * delta - alpha * (Man - M)) can approach zero under extreme conditions. Must be clamped to minimum absolute value before division.
- **delta tracks sign of dH, not sign of H.** Common implementation error. Delta encodes direction of field change; determines which branch of hysteresis loop is traversed.
- **Bias oscillator is not filtered out before J-A stage.** Participates in the nonlinear integration. Adding it after J-A produces physically incorrect result.
- **Parameter values in library are starting points, not production values.** Require fitting against measured B-H curve data. Ampex 456 is calibrated first; all others calibrated relative to it.

## Constraints and Dependencies
- **Stochastic Injection system:** Two confirmed injection points — He field perturbation before J-A integration (scaling with H, shaped by J-A nonlinearity) and particulate noise on M after integration but before playback differentiation. AC bias noise excluded. Phase 1 browser prototype does not need these; Phase 3 concern.
- **Sciotube (sibling plugin):** UI conventions, visual style, harmonic spectrum display, and control layout are direct reference for Sciotape prototype UI. Architecturally independent but visually a sibling.
- **JUCE framework:** Phase 3 depends on JUCE/C++ implementation framework being established for the suite.

## Reference Materials
| Book | Role |
|------|------|
| Bertram (1994) | Head field equations, gap loss derivation, nonlinearity chapter |
| Westmijze (1953) | AC biasing theory, physical justification for bias/hysteresis interaction |
| Begun (1949) | Transfer characteristics, AC bias fundamentals (in project files) |
| Jorgensen (1970) | IEC/NAB EQ time constants, measurement standards |
| Lowman (1972) | Real machine parameters for Ampex 456 at 15 ips — primary calibration target |
| Dempwolf et al. DAFx 2011 | Primary external source for J-A parameter curve-fitting (not in Google Drive library) |

## Open Questions
- Phase 1 prototype unbuilt and unvalidated — B-H loop shapes and harmonic signatures not yet confirmed for any of the 5 models.
- Bias frequency for Phase 1 not finalised — design doc specifies 40–60 kHz at 4x OS; exact value not chosen.
- EQ time constant accuracy for Phase 1 unspecified — full IEC/NAB table vs simplified single speed for initial validation.
- No listening validation against real tape machine recordings — qualitative character descriptions derived from parameters only, not verified.
- Saturator folder contents not confirmed — Claude Code prompt assumes existing tube saturator HTML file for UI reference; exact filename needs verification before running prompt.
