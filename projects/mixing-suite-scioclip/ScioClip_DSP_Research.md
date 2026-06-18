# ScioClip — Peak Control DSP Research

**Scionaugh Mixing Suite — Stage 2 DSP Research (consolidated, build-ready)**
Multi-mode clipper / limiter. Context-flexible: track, bus, master. Supersedes the earlier ScioClip draft.

ScioClip is a multi-mode peak processor: a clean digital lane (clipper plus true-peak brickwall limiter) and two circuit-modelled analogue limiter modes, selectable behind one face in the same way ScioGlue puts four compressor topologies behind one face and ScioMB composes engines. Every mode shares one scaffold (input drive, ceiling, output/unity, mix, oversampling, Aliveness, metering) and swaps only the gain-control engine underneath.

---

## 1. Design philosophy alignment

Every other Scionaugh nonlinearity is a physical device model. A clipper is different in kind: hard clipping is a mathematical function, not a device. This is resolved honestly rather than fudged.

- **Clip modes** are anchored on the clip function itself, made alias-free by **antiderivative antialiasing (ADAA)**, a published exact technique. ADAA lets us render the exact clip function in continuous time rather than approximating it with `tanh`. This is the clipper equivalent of choosing exact prewarped shelf coefficients over Simper's linear approximation in ScioEQ: we are not curve-fitting a nonlinearity, we are exactly evaluating a piecewise function and removing its aliasing analytically.
- **True-peak limit mode** is a DSP control system (lookahead gain reduction), documented as such, not pretending to be hardware.
- **Analogue limit modes** reuse and extend real device models: the Fairchild 670 variable-mu tube circuit and the Neve 2254 diode-pair shunt circuit, both derived from primary sources.
- **Detail foldback** (texture preservation) is grounded in the frequency-dependent saturation of real output transformers, so it is a §2.1 physical model, not a §2.1a hybrid trick.
- The only genuinely non-physical stage is the optional loudness **Optimize** make-up, which is a §2.1a hybrid and is labelled as such; recommended deferred out of v1.

Honesty status by mode:

| Mode | Anchor | Status |
|---|---|---|
| Hard / Soft / Cubic clip | ADAA on the exact clip function | Honest: function is the device (op-amp rail / diode clamp); ADAA is the faithful continuous-time render |
| True-Peak Limit | Lookahead gain computer; shared `LoudnessMeter` true-peak detection (BS.1770) | Honest: DSP control system, documented |
| Clip → Limit | Composition of the above | Honest: composed engines (ScioMB precedent) |
| Fairchild 670 vari-mu | 6386 remote-cutoff triode WDF (DAFx-12) | Honest: published circuit model, datasheet-fitted tube |
| Neve 2254 diode-pair | HBX31 shunt attenuator from the Neve schematic | Honest: derived from primary-source schematic |
| Detail foldback | Frequency-dependent transformer saturation (Paiva/Pakarinen) | Honest: physical model |
| Optimize make-up | Clean upward gain on low-level content | Hybrid (§2.1a), labelled; defer to v2 |

---

## 2. Reference plugins (UI / behaviour targets, not models)

These define the feel of the clean digital lane. They are behavioural references, not things to clone.

- **FabFilter Pro-L 2** sets the metering and limiter UI language: a real-time scrolling display showing input, output, gain reduction (shaded), and a loudness curve together, with peak gain-reduction value labels drawn onto the limiting regions, comprehensive LUFS/true-peak metering, and a unity-gain audition toggle. ScioClip's limiter UI follows this language using the suite's shared `LoudnessDisplay`.
- **Newfangled Flatline / Saturate** sets the clipper UI language: a **Shape** morph between a hard clip and the smoothest possible curve, with a live transfer-curve display showing the signal sitting on the knee.
- **Flatline / Elevate spectral clipper** is a more advanced idea worth noting but not the v1 default: it treats sine components at different frequencies independently so large low frequencies do not crowd out finer detail and the output tonal balance matches the input, with only harmonics added. This per-frequency independence is an optional future advanced clip mode; it has no published physical grounding and is more expensive. Not in v1.

---

## 3. Clip functions (exact, published forms)

### 3.1 Hard clip
```
f(x) = clamp(x, -L, +L)
     = -L            if x < -L
     =  x            if -L ≤ x ≤ +L
     = +L            if x > +L
```
`L` is the clip ceiling (linear). Op-amp-rail / diode-clamp behaviour. Infinite-bandwidth harmonics, so it cannot be evaluated naively at host rate.

### 3.2 Soft clip (cubic — primary soft mode)
```
f(x) = x - x³/3      for |x| ≤ 1
f(x) = sign(x)·2/3   for |x| > 1
```
Scaled to ceiling `L`: substitute `x → x/L`, multiply output by `L`. Chosen as the primary soft mode because its antiderivatives are elementary (clean ADAA, simple fallbacks). A `tanh` soft-clip is admissible as a secondary flavour (antiderivative `ln(cosh(x))`), offered if listening tests want a softer asymptote.

### 3.3 Asymmetry
Independent positive/negative ceilings `L+`, `L-`, reusing the ScioTube asymmetry concept. Asymmetric clipping introduces even harmonics (H2), matching the suite's harmonic vocabulary and the shared HarmonicAnalyzer. Requires DC blocking downstream (§9).

---

## 4. Antialiasing the clip — ADAA (core technique)

**Source chain:** Parker, Zavalishin, Le Bivic (DAFx-16); Bilbao, Esqueda, Parker, Välimäki (IEEE SP Letters 24(7), 2017); Chowdhury reference implementation (`jatinchowdhury18/ADAA`, BSD-3).

### 4.1 First-order ADAA
For nonlinearity `f(x)` with first antiderivative `F₁(x) = ∫f dx`:
```
            F₁(x[n]) − F₁(x[n-1])
y[n]  =  ───────────────────────────         when |x[n] − x[n-1]| > ε
              x[n] − x[n-1]

y[n]  =  f( (x[n] + x[n-1]) / 2 )             fallback when |x[n] − x[n-1]| ≤ ε
```
`ε ≈ 1e-5`. The fallback (evaluate `f` at the midpoint) avoids the `0/0`.

**Hard-clip antiderivative:**
```
F₁(x) =  x²/2                  for |x| ≤ L
F₁(x) =  L·|x| − L²/2          for |x| > L
```

**Cubic soft-clip antiderivative:**
```
F₁(x) =  x²/2 − x⁴/12          for |x| ≤ 1
F₁(x) =  (2/3)·|x| − 1/4       for |x| > 1     (×L scaling applied outside)
```

### 4.2 Second-order ADAA
Uses the second antiderivative `F₂` and a first-moment term (Bilbao 2017 gives the three-point form with its fallbacks). Stronger alias suppression at host rate, more divide-by-zero branches. Path to alias-free hard clip at 1×–2× rather than needing 8×–16×.

### 4.3 Recommended strategy: ADAA + modest oversampling
ADAA kills low-frequency aliases efficiently but is weak in the top octave; oversampling is the opposite. They are complementary (Chowdhury, Vicanek both converge on this).

> **Default (confirmed): first-order ADAA inside a 2× oversampled path, in every mode including Clip→Limit.** First-order + 2× is sufficient across the board; the Clip→Limit chain does not warrant second-order on the clip. User OS options: Off (ADAA only, lowest CPU), 2× (default), 4× (ADAA + 4×, for extreme drive). Mirrors the suite's existing Off/2×/4× convention (§2.2 of principles). Second-order ADAA is not used in v1 (kept in reserve only if a future swept-sine test surfaces an audible artefact).

ADAA state (`x[n-1]`, `F₁(x[n-1])`) is per-channel filter state: reset in `prepareToPlay()`, preserved across blocks.

---

## 5. True-peak brickwall limiter

### 5.1 Detection — reuse the shared `LoudnessMeter`
**ScioClip does not implement its own true-peak detector.** The shared `scio::analysis::LoudnessMeter` (built for ScioSee, `Plugins/Shared/Analysis/LoudnessMeter.{h,cpp}`) already provides BS.1770 / EBU R128 metering: momentary/short-term/integrated LUFS, LRA, **4× oversampled true-peak dBTP** (12-tap polyphase windowed-sinc FIR, `kOsFactor = 4`, `kOsTaps = 12`), and peak/RMS dBFS, with sample-rate-correct K-weighting from libebur128 coefficients. The header explicitly names the future Limiter/Clipper as a consumer.

**Two call sites, same math:**
1. **Displayed metering** uses the `LoudnessMeter` instance directly (observational, output bus), rendered via the shared `LoudnessDisplay`.
2. **The limiter's internal lookahead detector** reuses the *same* 4× polyphase true-peak FIR design (same coefficients, same calibration) but runs *predictively inside the gain loop*, because the limiter must duck before the inter-sample peak arrives, whereas the meter measures what already happened.

> **Resolved (implementation task for the ScioSee owner):** the lookahead detector and the meter **share one calibration** — the polyphase true-peak tap table is factored out of `LoudnessMeter::buildTruePeakCoeffs()` into a shared free function/header so both the predictive lookahead detector and the observational meter call the same coefficients. Two call sites, one source of calibration. No parallel copy.

The meter header reads BS.1770-**4**; we have been tracking BS.1770-**5** (Nov 2023). For true-peak and K-weighting the two are equivalent in practice — no change needed to the existing module. Note true-peak is an estimate: the standard only requires a method similar or better than its example FIR, so compliant meters disagree by ~0.2 dB. Give the ceiling a sensible default with headroom (−1.0 dBTP) rather than trusting an exact figure.

### 5.2 Lookahead gain computer
1. **Lookahead delay** `Lₐ` = **fixed 1 ms** (single value, no user control in v1) so the gain envelope ducks before the peak. Report `Lₐ + OS latency` via `setLatencySamples()` and feed it into the dry path of the `DryWetMixer` (`setWetLatency`) so Mix stays phase-aligned (§2.4).
2. **Peak detector** on the oversampled true-peak signal: `peak[n] = max over lookahead window`.
3. **Gain computer:** `g_req[n] = min(1, ceiling / peak[n])`.
4. **Smoothing / release:** attack equal to the lookahead time (dip reaches minimum as the peak hits). Release is **switchable between two laws** (user control): (a) **ScioGlue GR-proportional** auto-release (consistent with the suite, proven), and (b) a **peak-density-driven** release (loud without pumping — fast on sparse peaks, slower under dense limiting). Two-stage smoothing (fast + slow blended by GR depth) underlies both.
5. **Apply** `g[n]` to the delayed signal.

### 5.3 Optimize make-up (hybrid — deferred to v2)
The AL-1-style optimizer (upward compression on low-level content + intelligent makeup) is not a device: §2.1a hybrid. **Deferred to v2** so v1 stays anchored to the honest clip/limit core. When built, it must be labelled hybrid in code and dsp_notes and collapse to bit-exact bypass at neutral.

---

## 6. Analogue mode 1 — Fairchild 670 (variable-mu)

The tube mastering colour: smooth, program-dependent gain reduction, soft knee, harmonic richness from the 6386 tubes and the transformers. The "glue and weight" voice. Set-screw ratio runs 2:1 (compressor) to 30:1 (limiter); at high ratio the vari-mu topology *is* a limiter, which is the mode ScioClip uses. Pure-model recoverability: un-pinning the ratio recovers the compressor, per the parameter-freeing rule.

**Source:** Raffensperger, *Toward a Wave Digital Filter Model of the Fairchild 670 Limiter*, DAFx-12 (in the research folder, `dafx12_submission_9-fairchild.pdf`).

### 6.1 6386 remote-cutoff triode model
The 6386 is variable-µ: its AC µ depends on the DC grid-cathode voltage (remote cutoff). Existing 12AX7-style models (Koren) do not capture this, so the 670 needs its own tube equation. Anode current (amps):
```
Ia = p1 · Vak^p2 · (p3 − p4·Vgk)^p5 · [p6 + exp(p7·Vak − p8·Vgk)]
```
with Vgk = grid-cathode voltage (V, negative in the 670, so grid current is negligible), Vak = anode-cathode voltage (V).

| param | value |
|---|---|
| p1 | 3.981 × 10⁻⁸ |
| p2 | 2.383 |
| p3 | 0.5 |
| p4 | 0.1 |
| p5 | 1.8 |
| p6 | 0.5 |
| p7 | −0.03922 |
| p8 | 0.2 |

Fitted by Levenberg-Marquardt plus hand-tuning to the GE 6386 datasheet curves. This is the same datasheet-fit-then-derive approach as Koren, so it sits inside the suite philosophy.

### 6.2 Signal amplifier (WDF)
Class A variable-µ stage, modelled as a wave digital filter (bilinear transform = trapezoidal integration of the circuit ODEs). One channel modelled; two tubes per half (current ×2; the model scales to 6/8 tubes at no cost).

- Input step attenuator: linear `Ain ≤ 1`.
- `RL1 = RL2 = 100 kΩ` (sidechain resistive component + grid impedance).
- Balance simplified: `R11 = R21 = 705 Ω`, `Vbal = −3.1 V` (software has perfect stability, so no balance knob needed).
- Metering sense resistors `R12 = R22 = 33 Ω` (excluded as superfluous).
- Bypass cap `C1 = 4 µF`; input transformer `Np/Ns = 9`, output transformer `Np/Ns = 1/9`. Both transformers: `Rc = 10 kΩ`, `Lm = 35.7 H`.
- `Rout = 600 Ω` (downstream load), `Rsc = 1 kΩ` (sidechain input).
- Grid voltages: `Vg1 = VRL1 + Vx`, `Vg2 = −VRL1 + Vx`, where `Vx(t) = Vsc(t−1) + Vbias`, `Vbias = −7.2 V` (zero-adjust). Anti-phase input transformer means `VRL2 = −VRL1` (compute one).
- Output transformer drives each grid through `Np/Ns = 1/4.5` (half the winding per grid).

Transformer WDF parameters (input / output / sidechain):

| | Rp | Lp | Rs | Ls | Cw |
|---|---|---|---|---|---|
| Input | 10 Ω | 4 mH | 50 Ω | 1 mH | 210 pF |
| Output | 5 Ω | 100 µH | 1 Ω | 400 µH | 1 pF |
| Sidechain | 10 Ω | 2 mH | 50 Ω | 1 mH | 10 pF |

The tube integrates into the WDF via implicit equations solved by Newton-Raphson:
```
Vak(t) + 2·R0·Ia[Vgk(t), Vak(t)] − a(t) = 0      (solve for Vak)
b(t)   = Vak(t) − 2·R0·Ia[Vgk(t), Vak(t)]
```
`R0` is the port resistance seen by the Tube box. `Vgk` uses the current grid voltage and the *previous-sample* cathode voltage (small error; cathode moves slowly). The `C1` bypass forms a delay-free loop; resolve as the paper does — two `2·C1` caps in series with a bidirectional unit delay between them, preserving Class A symmetry and giving each WDF half a single nonlinearity.

Output: `Vout = Vout_push − Vout_pull`.

### 6.3 Sidechain
Hybrid WDF / black-box static nonlinearity (the paper replaces the sidechain tube amp with a fitted static curve). Sidechain input transformer: `Rin = 600 Ω`, `Rterm = 1360 Ω`, `Rload = 152 kΩ`, `Np/Ns = 1/17`, `Rc = 50 kΩ`, `Lm = 5.7 H`. **All six program-dependent time-constant settings are modelled in v1** (the faithful set), since the six TCs — including the two auto/program-dependent positions — are a defining part of the 670's character. Model them per-mode rather than sharing a generic detector.

### 6.4 Fidelity boundary (state honestly in dsp_notes)
The DAFx-12 model is a step toward a full WDF design: it uses a black-box sidechain rather than modelling those tubes, and the transformer parameters are fitted/measured, not taken from Fairchild's (unpublished) winding specs. So the Fairchild mode is faithful to the published model, which is faithful to the datasheet and to measurement — but it is not derived from Fairchild's own transformer data, because that data does not exist publicly.

---

## 7. Analogue mode 2 — Neve 2254 (diode-pair shunt limiter)

The solid-state British bus colour: faster and punchier than the Fairchild. Derived directly from the Neve schematic in the research folder (`Neve-2254-Limiter-Schematic.pdf`, drawing L/10,004 and sub-boards). Extracted values are archived in `neve2254_extracted.md`.

### 7.1 Topology correction
The 2254 is **not a four-diode Wheatstone bridge** (that is the later Neve 33609). The 2254 gain-reduction element on board **B185** is a **two-diode shunt variable attenuator** using a matched diode pair (part **HBX31**), acting as a current-controlled variable resistance shunting the audio path. The shaping network sets the diode bias from the sidechain control current.

### 7.2 Gain-reduction element (B185, drawing D/10,022)
- Audio in via **31267 input transformer** (U/V differential), through matched `15K + 22K` series resistors per leg, with `2K7` and `12K` bias resistors.
- **HBX31 diode pair** shunts the path; `300p` shunt caps per leg; `5µF/64` coupling.
- Two **BC107** low-noise buffers (`2K7` emitter resistors, `15K` loads).
- Out via **10468 output transformer** to R/N/T/P (`22K` across N/T).

### 7.3 Shaping network (control current → bias voltage)
- Control transistor pair **2N1711 / 2N1132** plus BC107.
- `470 + 47` series; `1000p`; `39K`; `4K7`; `220`; `15K`; `1K8`; `560`; `400µ/25`; `100µ/10`; `22µ/15`.
- Input clamp: `BAX16 + 7K5`.

### 7.4 Sidechain
- **31267 sidechain transformer**.
- Detector: `2× BAX16`, `HS2027` zener, `100K`, `1K8`, `560`.
- Top-level attack 100 / 200 / 800 ms (networks `8K2` / `18K` / `82K`); limit recovery AUTO (`68K` / `3K9` / `125µF`). Fixed-attack feel comes from the sidechain RC + transformer, not a bridge.

### 7.5 Makeup amplifier (BA340, drawing EX 10,340)
Discrete transistor amp after the gain-reduction stage: input TR1 (BC214KC) / TR2 (BC184C); gain TR3–TR8 (BC184C / BC461-6 / BC441-6); diodes D1/D2 (BAX13), D3 (AA144); full RC values archived in `neve2254_extracted.md`.

### 7.6 Modelling notes / boundary
- Model the HBX31 as a generic diode pair (Shockley equation) fitted to the shunt-attenuator transfer, since exact HBX31 curves are not on the sheets. State this in dsp_notes.
- If implemented as a WDF, ADAA applies to the diode nonlinearity (Albertini/Bernardini/Sarti, *Antiderivative Antialiasing in Nonlinear WDFs*, DAFx-20).
- Confirm the 2254 fixed-attack value against the manual text (memory: ~5 ms).

---

## 8. Brickwall safety toggle

The analogue modes are faithful, so fast peaks can slip past them (true to the hardware). A per-mode **Brickwall** toggle composes the two lanes:

- **Off:** faithful Fairchild / Neve behaviour; peaks may pass.
- **On:** the §5 true-peak digital brickwall runs after the analogue stage and catches whatever the circuit let through, guaranteeing the ceiling.

This is a clean composition (ScioMB precedent), gives the best of both, and is very usable for mastering.

---

## 9. Detail foldback (texture preservation) — physically grounded

The "reflect the clipped signal to keep texture" idea. The Mannix-style implementation is: clip the main signal, and in parallel re-inject a high-passed copy of the *pre-clip* signal so transient/HF detail the clipping removed is restored.

**Physical grounding (this is the key result that moves it from §2.1a hybrid to §2.1 physics):** real output transformers introduce distortion at low frequencies only — Paiva, Pakarinen, Välimäki, Tikander (*Real-Time Audio Transformer Emulation*, EURASIP J. Adv. Signal Process. 2011, open access, in the research folder) measured this at below ~100 Hz (Fender) and ~30 Hz (Hammond). The iron saturates in the lows; the highs pass comparatively clean. So re-injecting a high-passed copy around a saturating stage mirrors how transformer iron actually behaves. The frequency-dependent J-A model (eddy-current + anomalous-loss terms) is the formal version; the same Jiles-Atherton core already used in ScioTape.

**Structure:**
```
out = clip(x) + foldback · HPF_minphase(x, fc)
```
- `HPF_minphase` is a **minimum-phase FIR** built via the real-cepstrum (Pei-Lin) or Hilbert (Damera-Venkata) methods in the research folder — minimum-phase so the detail path is low-latency and free of pre-ring, which would otherwise smear the very transient it is meant to preserve.
- Gain-stage carefully: the high-passed signal can push back above the ceiling; foldback must be bounded.
- `fc` adjustable (≈500 Hz–12 kHz): restore just transient snap, just air, or a wider presence band.

**Per-mode defaults** (your decision): both analogue modes have output transformers, so foldback is physically justified for both, but the character differs because the Fairchild iron (11 transformers) and the Neve iron (1166 in, 10468/LO1166 out) differ. Pin a sensible foldback default per voicing rather than one shared value; expose the control for the digital clip modes where the user drives it directly.

---

## 10. Mode architecture (one plugin, all modes)

Follow ScioGlue exactly: a **Mode** selector with **per-mode prefixed parameter IDs** so inactive modes retain values for automation and preset stability (§4.3); enums attach by slug via `ComboBoxAttachment`, never by index. Each mode is a small engine class behind a common `processBlock` dispatch (the ScioMB micro-rack pattern).

Mode list (v1):
1. **Hard Clip** — ADAA hard clip; drive, ceiling, asymmetry.
2. **Soft Clip** — ADAA cubic (optional tanh); drive, knee, ceiling.
3. **True-Peak Limit** — lookahead brickwall; shared true-peak detection; auto-release.
4. **Clip → Limit** — clip stage feeding the limiter (the modern-master chain): clip catches fast transients cheaply, the limiter guarantees the ceiling. Single most useful loudness mode.
5. **Fairchild Vari-Mu** — §6.
6. **Neve 2254** — §7.

Cross-cutting controls in all modes: **Input drive**, **Ceiling**, **Output / Unity** (Unity auto-applies the inverse of input gain to the output for level-matched A/B — the AL-1 trick, cheap and popular), **Mix** (parallel via `DryWetMixer`, latency-compensated), **Oversampling** (Off/2×/4×), **Aliveness**, **Brickwall** toggle (analogue modes), **Detail Foldback** (amount + `fc`). Limit-bearing modes also expose a **Release law** switch (GR-proportional / peak-density). Lookahead is fixed at 1 ms (not a control). **Optimize** deferred to v2.

Param-ID prefixes: `clip_*`, `lim_*`, `vmu_*`, `neve_*`, plus shared `in_drive`, `ceiling`, `mix`, `os`, `alive`, `fold_amt`, `fold_fc`, `lim_release_mode`.

---

## 11. Aliveness (Stochastic Injection)

Per §3 of the principles doc: physically-motivated, zero-at-silence, zero-at-no-action, hard-gated at knob 0 (conditional branch, not multiply-by-zero). Shared knob mapping `(knob/10)^0.4`, lower-range-weighted; `seed_L = instanceSeed`, `seed_R = ~instanceSeed`, only `instanceSeed` serialised. No new generators — `BandlimitedWhiteNoise` / `WhiteNoise` cover it.

Injection sites by mode:
- **Clip modes — threshold micro-jitter.** A real op-amp/diode rail wobbles with thermal drift and supply ripple. Inject a signal-dependent perturbation on the **ceiling `L`** itself, scaled by current overshoot `max(0, |x| − L)`. Zero when nothing is clipping. Modulates *where* the clip happens, varying the harmonic edge — the correct physical site, analogous to grid-voltage injection in ScioTube. Bandlimited white ~8 kHz, L/R independent.
- **Limit modes — gain-coefficient jitter.** As in ScioGlue: perturb the gain-reduction coefficient before it is applied, scaled by `|GR_dB|`, zero at no reduction (see `SICompressor.md` §2–3). Bandlimited white.
- **Fairchild / Neve modes** — inherit ScioGlue's per-topology injection unchanged (vari-mu and the diode/transistor control element respectively).

**Do not** inject additive noise on the limiter output to "sound analogue" — that violates zero-at-silence and the §3.1 site rule. Injection is on the threshold or the gain coefficient, never the output.

---

## 12. Visualisation (physics-grounded, §5 of principles)

- **Transfer curve** (input→output) from the active mode's actual function: bent line for hard clip, soft curve for cubic, flat-top for limit, the vari-mu/diode characteristic for the analogue modes. Static, reads APVTS, like ScioTube's `evaluateTransfer()`.
- **Activity scope:** clip modes show the clipped region overlay (Delta-style, like ScioGlue's Delta Ghost Waveform); limit modes show the GR envelope over a BPM-synced window (reuse ScioGlue's GR scope).
- **Loudness / true-peak metering:** the shared `LoudnessDisplay` (Pro-L-style scrolling LUFS + dBTP), fed by the shared `LoudnessMeter`. ScioClip owns the ceiling so it carries the full meter.
- **HarmonicAnalyzer** (H2/H3/H4) on the clip output, since clipping is a harmonic generator like the other saturators.

UI uses `ScionaughLookAndFeel` / `ScionaughColours` / `MetalPalette`, colours never hardcoded. Pick an unused accent within the shared chrome (EQ cyan, EQP amber, MB purple → suggest red/crimson, which also reads as "ceiling / danger").

---

## 13. JUCE implementation notes & gotchas

- **Latency reporting is mandatory.** Total = lookahead + OS latency. Report via `setLatencySamples()`; push the same delay into the `DryWetMixer` wet/dry path so Mix and the Brickwall composition stay phase-aligned. Recompute and re-init on any OS-factor change.
- **DC blocking** after asymmetric clip (asymmetry adds DC): one-pole `y = x − x₋₁ + 0.995·y₋₁` (§2.3).
- **Output normalisation:** clip level rises with drive; compensate so default drive ≈ unity (§2.5). Cubic unity-region gain is 1, so this is mostly a drive trim.
- **ADAA state** (`x[n-1]`, `F₁(x[n-1])`) is per-channel filter state; reset in `prepareToPlay()`, preserve across blocks.
- **WDF analogue modes** (Fairchild, Neve-as-WDF): the suite already fetches `chowdsp_wdf` (see ScioSee CMake); reuse it. Newton-Raphson for the implicit tube/diode equations; watch convergence near zero (Taylor fallback for Langevin-type terms if the Neve diode model needs it).
- **RT-safety:** no allocation/locks on the audio thread; static state; heavy prepare in `prepareToPlay`. DSP writes `std::atomic` / lock-free SPSC rings; 30 Hz UI timer reads and repaints; `paint()` never touches DSP state.
- **Reuse, don't re-derive:** true-peak FIR from `LoudnessMeter`; auto-release ballistics from ScioGlue; GR scope and Delta overlay from ScioGlue; spectrum/Harmonic displays from the shared Analysis/Visualization layer.

---

## 14. Source references

**True-peak / loudness**
- ITU-R BS.1770-5 (11/2023) and BS.1770-4 (2015, equivalent for TP/K-weighting). Shared `LoudnessMeter` already implements detection.
- EBU R128 / Tech 3341–3344 — ceiling targets (−1 dBTP common).

**ADAA (clip core)**
- Parker, Zavalishin, Le Bivic, DAFx-16. Bilbao, Esqueda, Parker, Välimäki, IEEE SP Letters 24(7), 2017. Esqueda et al., EUSIPCO 2016. Holters, DAFx-19 (stateful). Albertini, Bernardini, Sarti, DAFx-20 (diode-bridge WDF). Zheleznov, Bilbao, DAFx-24. Vicanek (vicanek.de). Chowdhury (2020) + `jatinchowdhury18/ADAA` (BSD-3).

**Fairchild 670** (research folder)
- Raffensperger, *Toward a WDF Model of the Fairchild 670 Limiter*, DAFx-12 — 6386 equation, WDF values, sidechain.
- Fairchild 670 instruction manual; elektrotanya + radiomuseum schematics.

**Neve 2254** (research folder)
- `Neve-2254-Limiter-Schematic.pdf` (L/10,004 + B185/B191/B192/BA340 sub-boards). Extraction: `neve2254_extracted.md`.

**Transformers / detail foldback** (research folder)
- Paiva, Pakarinen, Välimäki, Tikander, *Real-Time Audio Transformer Emulation*, EURASIP 2011 (open access) — "distortion at low frequencies only".
- Jiles-Atherton (JMMM 1986); Jiles-Thoelke-Devine (IEEE Trans. Mag. 1992); frequency-dependent J-A.

**Minimum-phase FIR** (research folder, for foldback HPF + any internal filters)
- Damera-Venkata, Evans, McCaslin (IEEE TSP 2000, Hilbert). Pei, Lin (IEEE TCAS-II 2006, real cepstrum). Oppenheim & Schafer.

**Suite engines reused**
- `mixing_suite_principles.md` §2.1/§2.1a/§2.2/§2.4/§2.5/§3/§4.3/§5.
- `SICompressor.md`, `StochasticInjection.md`, `SI_Implementation.md`, `StochasticEngine.h`.
- ScioSee shared layer: `Shared/Analysis/LoudnessMeter`, `Shared/Visualization/LoudnessDisplay`, `chowdsp_wdf`. ScioGlue: vari-mu engine, auto-release, GR scope, Delta overlay.

---

## 15. Resolved decisions (Stage 3 inputs)

All Stage-2 open questions are resolved. Recorded here as decisions for the build.

1. **True-peak detector / shared `LoudnessMeter`:** shared calibration, two call sites. Factor the polyphase true-peak tap table out of `LoudnessMeter::buildTruePeakCoeffs()` into a shared header so the predictive lookahead detector and the observational meter use identical coefficients. *Implementation task for the ScioSee owner; no parallel copy.*
2. **ADAA order / OS:** first-order ADAA + 2× as the default in **every** mode including Clip→Limit. Off/2×/4× user options. Second-order held in reserve only if a swept-sine test later surfaces an audible artefact.
3. **Lookahead:** fixed **1 ms**, no user control in v1.
4. **Limiter release:** **switchable between two laws** — ScioGlue GR-proportional (default, suite-consistent) and a peak-density-driven release (loud without pumping). Exposed as `lim_release_mode`.
5. **Optimize make-up:** **deferred to v2.** v1 ships only the honest clip/limit/analogue core.
6. **Neve diode model:** generic Shockley diode pair fitted to the HBX31 shunt-attenuator transfer (HBX31 curves unavailable). WDF implementation so DAFx-20 ADAA applies. *Remaining task: confirm the 2254 fixed-attack value (~5 ms) against the manual text — a lookup, not a decision.*
7. **Fairchild time constants:** **all six** program-dependent TC settings modelled in v1 (faithful set).
8. **Spectral (per-band) clip mode:** not in v1; parked for a later version.

Two residual lookups (not decisions, do not block Stage 3 kickoff): the 2254 fixed-attack value (item 6) and the factoring of the shared true-peak coefficients (item 1).
