# ScioMB — Code Context

<!-- Only read when asked for deeper evaluation or cross-project analysis -->

## Architecture Overview
JUCE plugin. Crossover (linear, native rate, outside OS): Minimum Phase = ZDF SVF serial segment tree with O(K) merge-side allpass compensation + LR2 sign rule; Linear Phase = telescoping Kaiser-FIR segment kernels with shared input FFT. Per-band chain at OS rate: M/S encode -> [Comp(GR only) <-> Sat(drive only)] in chain order -> M/S decode -> terminal gain + static auto-makeup (native rate). Single global 26-channel Oversampling wrap. Plugin-level DryWetMixer. Frequency-matched external sidechain (cheap IIR, bulk-delay tracking reported latency). Composite mode-dependent Aliveness.

## Key Technical Decisions
- Free placement = contiguous segment tree; perfect reconstruction proven by induction (each edge's LR identity); compensation done O(K) at the merge.
- Discrete slopes {6,12,24,48} only — continuous slope blends provably null at crossover (rejected).
- Diode-bridge: Shockley small-signal r_d gain law, progressive onset, in-path tanh colour zero at GR=0, 2nd-order AUTO with internal nonlinearity. db_ slug.
- Transformer: inverse Jiles-Atherton (B-in/H-out, delM=1) in a chowdsp_wdf two-port with leakage L + winding C; BJT Ebers-Moll front stage; voicings as parameter sets over one code path.
- Enums serialised by string slug (append-safe). Single static param default (e.g. attack 10 ms) — per-topology preferred values live in presets, not defaults.

## Dependencies on Other Projects
- Ports ScionaughCompressor topologies, ScionaughTube/Tape saturation, ScionaughEQ SVFBand.h, ScionaughSpatialiser M/S; reuses ScionaughEQP WDF/WBLT pattern and StochasticEngine.
- chowdsp_wdf (shared with ScioEQP).

## Known Issues / Technical Debt
- [CALIBRATE] constants still placeholder: diode ideality n + colour depth, transformer core params per voicing, API 2503/2622 turns ratios, SSL Jensen transformer data, interpolated Aliveness ceilings (db_ 0.002, transformer pink 0.0015).
- MIN_EDGE_RATIO (1.15) is a provisional tuning value.

## Open Questions
- Final calibration against measured hardware once B-H loops / transformer specs are obtained.
