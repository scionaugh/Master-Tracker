# ScioMB — Chat Context

<!-- Only read when asked for deeper evaluation or cross-project analysis -->

## Goals and Purpose
Multiband dynamics + colour processor. Up to six freely-placed bands, each an independent micro-rack: a selectable compressor topology + an optional selectable saturation slot, behind one variable-slope band-splitting front end. The first suite plugin to compose existing Scionaugh engines per-band rather than introduce one monolithic model.

## Key Decisions Made
- Free-placement bands (Pro-MB model) resolve internally to a contiguous segment tree; gaps are passthrough segments.
- Global phase mode (Minimum/Linear) — perfect reconstruction is a whole-system property, not per-crossover. Dynamic Phase parked.
- One terminal band gain (resolves the comp-makeup vs sat-output-gain conflict); chain-order swap is one enum driving both UI and routing.
- Three [NEW DSP] items: variable-slope crossover, diode-bridge compressor (Neve 2254), transformer saturation (inverse J-A + WDF, Neve/API/SSL voicings). Everything else reuses validated suite engines.
- §11 UI open questions all resolved (slope picker hides LR2; shared-edge handle writes both params; slope on handle + editor; auto-makeup inline readout; deep-panel gear icon; GR shaded fill + per-tab number).

## Constraints and Dependencies
- Reuses ScionaughCompressor (4 topologies), ScionaughTube, ScionaughTape, ScionaughSpatialiser (M/S), ScionaughEQ (SVF), ScionaughEQP (WDF/WBLT precedent), StochasticEngine.
- Docs in Plugins/ScionaughMB/: ScioMB_design_notes.md (Stage 1), dsp_notes.md (Stage 2), ScioMB_UI_notes.md (Stage 3), ScioMB_CodePrompts.md (P0-P8).

## Open Questions
- Deep-panel affordance parity with ScioEQ/ScioEQP (UI §11 Q5).
- The [CALIBRATE] items needing measured hardware data (transformer B-H loops, API/SSL transformer specs, diode bridge identity).
