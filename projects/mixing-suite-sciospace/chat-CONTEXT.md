# ScioSpace — Chat Context

## Goals and Purpose
A vertical spatialiser that places sounds convincingly from Tailbone (−90°) to Up The Head (+90°) without timbral damage. The core design principle — no processing on the mid channel, all spatial work on the side channel only — is what allows it to work on dense electronic mixes where conventional HRTF spatializers destroy punch and timbre.

## Key Decisions Made

## Convolution Reverb Module (beta v3, finalized 2026-06-11)

**Mid channel must remain untouched** - same core constraint as the rest of ScioSpace. Reverb processing is confined to the side channel (Mono-Safe mode) or a ducked mid channel (Expanded mode).

**Engine:** `juce::dsp::Convolution`. Non-uniform partitioned convolution for the Scoring Stage IR (long tail), zero-latency for Plate and Wood Room (short tails).

**Two-mode routing toggle:**
- Mono-Safe: side channel only, reverb cancels in mono
- Expanded: ducked mid channel (envelope-follower based), reverb audible in mono

**IR switching:** dropdown selection (Off / Plate / Wood Room / Scoring Stage), no continuous morphing. A 5-10ms fade on switch avoids clicks. Loudness matched across all IRs so switching rooms doesn't cause level jumps.

**"Acoustic Material" continuous slider - dropped.** IR selection is dropdown-only, no morphing between IRs.

**Pre-delay and ducking are hardcoded per room** (no exposed user controls). Ducking attack is fixed at 2ms across all rooms (transient-driven, room-independent). Pre-delay and ducking release scale with each IR's length (room-dependent, not tempo-dependent - transients like kick/hi-hat don't change shape with BPM):

| Room (IR) | IR length | Pre-delay | Ducking attack | Ducking release |
|---|---|---|---|---|
| Plate (mono-safe) | 35ms | 5ms | 2ms | 50ms |
| Wood Room (borderline) | 80ms | 10ms | 2ms | 120ms |
| Scoring Stage (wide) | 600ms | 30ms | 2ms | 400ms |

**Mono-safe indicator - single threshold, folded into the wireframe room UI** (see UI design below), not a separate three-tier light. Mono-safe = corr > 0.7.

**IR pre-processing requirements:** coincident capture, HPF at 150 Hz baked in, truncated to 2048-4096 samples, no modulation.

**Plugin targets mix-bus use at 96kHz.**

### UI Design (finalized 2026-06-11)

Top bar: existing ScioSpace branding and preset dropdown, plus a new Room dropdown (Off / Plate / Wood Room / Scoring Stage) and a two-position Mono-Safe/Expanded toggle.

XY pad (left ~60%): unchanged grid, axis labels, corner rivets, and brass puck for pan/elevation/wideness. When a room is selected, a wireframe box appears centered on the puck. Box size follows a log scale (small for Plate, medium for Wood Room, near-pad-width for Scoring Stage). Outline is solid for mono-safe rooms, dashed for non-mono-safe. The pad acts as a window into a larger space - anything the box extends past the pad edges simply isn't drawn (no clamping/repositioning). Selecting "Off" removes the box. Switching rooms animates the box to its new size over ~150-200ms, timed with the audio crossfade.

Vectorscope + correlation meter (right ~40%): visually unchanged, but the correlation meter now reflects live post-processing correlation including the reverb's contribution - doubles as real-time mono-compatibility feedback for the current room + position combination.

Bottom knob row: unchanged (Wideness, HRTF, Noise, Depth).

**Architecture derived from reverse-engineering Plutonium XTra**, not from the original research paper. The paper proposed HRTF high-frequency notching (5–10 kHz); measurement revealed the actual mechanism is low-frequency side-channel phase/amplitude manipulation (5–200 Hz only, flat above).

**Three-component architecture (side channel only, mid untouched):**
1. Low-frequency minimum-phase shelving/resonant network (5–200 Hz) — two cascaded second-order sections, primary lobe ~45–50 Hz, secondary lobe ~100 Hz. Polarity flips between Tailbone and Up The Head.
2. Fixed-level broadband stochastic noise at ~−59.4 dB (signal-relative) — position-independent SR component added to side channel only.
3. Mild position-dependent nonlinearity on side channel — subtle harmonic density shift with body position.

**HRTF notch mechanism is Phase 2 (additive, not replacement).** Pinna-induced spectral notch (4.7–11 kHz, position-dependent) layered over Phase 1 architecture. HRTF Amount knob controls the blend — at 0% the notch is shallow (~−1.5 dB) and timbre is fully preserved; approaching 100% the notch deepens to −6 to −10 dB.

**Seven named body positions:** Up The Head (+90°), Head (+45°), Throat (+20°), Heart/Reference (0°), Stomach (−20°), Lower Belly (−45°), Tailbone (−90°).

(Early design sessions specced this as an 8-position "body spatial stage" — Above Head/Head/Throat/Chest/Stomach/Hips/Feet/Below Feet — as the final stage on EVERY plugin in the suite, tied to a "BST" architecture. That per-plugin staging idea was dropped in favour of a single standalone ScioSpace plugin with this continuous XY-pad positioning.)

**Velvet Noise Decorrelator (VND) is the planned upgrade** for the current Gaussian noise injection — 76–88% fewer CPU operations, no FFT required, multiplier-free.

**Mono-compatibility requirement:** Phase correlation coefficient must remain +0.3 to +0.7. FFT null test (mono sum) must have <0.7 dB ripple across 20 Hz–20 kHz.

## Constraints and Dependencies
- `Research/BST-Spatializer/RESEARCH.md` — primary architecture and psychoacoustics reference
- `Research/BST-Spatializer/Advanced Stereo Widening Techniques Research.pdf` — DSP white paper (M/S, all-pass networks, VND, IACC)
- CIPIC HRTF Database (UC Davis) — for Phase 2 empirical notch calibration
- `UI_Design_V1.md` — XY pad, vectorscope, correlation meter layout

## Open Questions
- Velvet noise upgrade: current ring-buffer approach works. VND still worth implementing for CPU efficiency (~76% fewer ops) — defer to post-testing
- Phase 2 HRTF notch: implemented in beta v2 using a parametric model (not CIPIC database). Whether CIPIC data would improve accuracy is a future question
- Body position: implemented as continuous XY pad parameter
- No SI specification document exists for ScioSpace — the decorrelation noise is handled separately from the Aliveness SI system
- Mono-safe threshold (corr > 0.7) is provisional - needs verification against real mix-bus material before finalizing
- IR library licensing not yet resolved for any third-party assets beyond the three generated prototypes (OpenAIR recommended as starting point for coincident-captured assets)
