# mixing-suite — Chat Context

## Goals and Purpose
The Mixing Suite is a set of "core module" plugins (Sciotube, Sciotape, ScioGlue, ScioSpace, and any future modules), each modelling a specific piece of analogue mixing gear with mathematical models only (no impulse responses — IRs flagged as a possible future addition if sourceable). Sold via the Scionaugh website (individually and as a bundle) and also used to standardise the user's own mixing workflow.

**Long-term roadmap:** once the core modules are mature, they'll be combined into pre-configured "specialised multi-FX modules" — e.g. HATZ Compressor, SNARE Processor, PERC Bus Processor, KICK & BASS Processor — each a curated chain of core modules for a specific mix element. These specialised modules are downstream products, not a separate/parallel development track. The original early-session specs for HATZ/SNARE/PERC/KICK&BASS (attack/release steps, ratio options, signal chains, multiband designs, etc.) describe that future product layer, not the current four core modules.

## Key Decisions Made

**Oversampling / quality modes:** Early sessions specced separate "physically accurate vs CPU-light" and "linear phase vs zero latency" toggles per plugin. This was superseded by the single Off/2x/4x oversampling choice now implemented consistently across all four core modules (default 4x).

**Stochastic noise → Aliveness (SI) system:** Early sessions framed tube "aliveness" as power-supply ripple, thermionic shot noise, and quantum effects layered into the saturation model. This became the suite-wide Stochastic Injection (SI) system (`StochasticEngine.h`), with per-plugin SI spec docs: SITube.md, SITape.md, SICompressor.md. SISpatialiser.md does not yet exist (ScioSpace has no Aliveness SI — its decorrelation noise is a separate mechanism).

**Tube model:** Norman Koren SPICE equations confirmed as the core saturation model — implemented in Sciotube (11 tube models, 1996-vintage parameter set). ScioGlue's Variable-Mu stage diverged from this: it uses a custom `muSat(x, grDb)` function rather than a named Koren tube model (see mixing-suite-scioglue CONTEXT for detail).

**Spatial stage → ScioSpace:** Early sessions specced an 8-position "body spatial stage" (Above Head/Head/Throat/Chest/Stomach/Hips/Feet/Below Feet) as the final stage on every plugin, tied to a "BST" architecture, informed by Plugin Doctor analysis of Plutonium (0v1 Records). This evolved into ScioSpace as a standalone spatialiser plugin with a continuous XY pad and seven named reference positions (Tailbone to Up The Head) — see mixing-suite-sciospace CONTEXT. The "final stage on every other plugin" framing was dropped in favour of one dedicated spatialiser.

## Constraints and Dependencies
- Plugin Doctor analysis of Plutonium (0v1 Records) — basis for ScioSpace's reverse-engineered spatial architecture
- Norman Koren SPICE articles (Parts 1 & 2) — Sciotube tube model parameters
- Acustica Audio INK — reference for future PERC-style multiband specialised module

## Planned Specialised Modules

**LOW LEADS Bus Processor (Plugin 5)** — design brief exists (`LOW_LEADS_Bus_Processor_Design_Brief.docx`, v0.1, prototype phase). Targets low-register synths/guitars; five-stage chain: M/S low-cut → 4-band multiband compressor (per-band topology + saturation) → full-frequency compressor → limiter → vertical spatial positioning (= ScioSpace). Architecturally shared with future HIGH LEADS and ATMOS processors. Not started — Phase 1 web prototype not yet begun.

- ScioGlue already covers the per-band topology selector (Optical/VCA/FET) and 4 of 5 full-frequency compressor shortlist candidates (1176 FET, LA-2A Optical, SSL G-Bus VCA, Fairchild 670 Variable-Mu). LA-3A (Optical/Solid-State) has no equivalent yet.
- Sciotube (Tube/Koren) and Sciotape (Tape) cover 2 of 4 per-band saturation models. SSL console character and "Classic Preamp" (Neve 1073 vs API 512, undecided) saturation models don't exist yet.
- "Vertical Spatial Positioning" stage = ScioSpace (the brief's "BST Vertical Spatial Audio Design Brief" is ScioSpace's design reference, not a separate document). Note the same naming/preset divergence already flagged in mixing-suite-sciospace CONTEXT (discrete body presets vs continuous XY pad).
- Global features table in the brief repeats the superseded accurate/light + linear-phase/zero-latency + live-off/export-4x oversampling framing — same divergence as noted above (superseded by Off/2x/4x).

**New core modules implied by LOW LEADS, not yet started:**
- Multiband compressor — per-band topology + per-band saturation selection (the "tonal heart" of LOW LEADS and the other planned bus processors). This is itself a core module, not something LOW LEADS builds standalone.
- M/S low-cut filter (independent Mid/Side cutoff, slope/order TBD)
- Limiter/clipper — soft knee / detail-preservation (Newfangled-style) / antiderivative anti-aliasing / oversampling-based

## Open Questions
- Which additional core modules (beyond Sciotube/Sciotape/ScioGlue/ScioSpace, and now the multiband compressor, M/S filter, and limiter implied by LOW LEADS) are planned, and in what order, before the specialised multi-FX modules become active development?
- Tube position in signal chain (input/VCA/output stage) — flagged in early sessions as needing prototyping; not yet resolved for current modules
- Will specialised modules need their own SI specs, or inherit from the core modules they're built from?
- LOW LEADS open questions (compressor topology shortlist, fourth saturation model, M/S filter slope, saturation pre/post compression, single vs. separate LOW/HIGH/ATMOS plugins, mono-compatibility check) all remain unresolved pending prototyping.
