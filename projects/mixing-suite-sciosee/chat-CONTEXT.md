# ScioSee - Chat Context

<!-- Only read when asked for deeper evaluation or cross-project analysis -->

## Goals and Purpose
ScioSound's dedicated metering / analysis plugin (product name ScioSee; code target ScionaughAnalyser). Unifies four views: spectrum analyzer, oscilloscope (scope), stereo analyzer, and loudness meter. First three are tabs; loudness is pinned to the right on every tab. The deeper purpose is architectural: ScioSee rides on shared analysis + visualization modules that the rest of the suite also uses, so improving analysis or visualization once propagates everywhere. ScioSee is both a product and the reference implementation of those shared modules.

## Key Decisions Made
- Two-layer shared architecture in Plugins/Shared/: Analysis (DSP-thread data producers) + Visualization (UI renderers). Extract-first, then retrofit EQ/MB/Glue/Space, then build ScioSee.
- Base + overlay pattern: shared display base, per-plugin extras (EQ band handles, MB crossover, Glue GR) layer on top instead of forking.
- Spectrum dual-engine: FFT-binned thirds inside EQ/MB (reuse existing FFT, free), true 1/3-octave filterbank inside ScioSee (DigiCheck fidelity). Shared display + shared calibration; parity is a hard requirement.
- SpectrumAnalyzer (EQ/EQP/MB/ScioSee) is SEPARATE from HarmonicAnalyzer (telemetry/Tape/Tube); may share an FFT core.
- Scope is PsyScope-style: Layer/Sum+Layer/Stack, per-layer invert, beat-sync vs free Hz/note, segment readouts, latency-comp nudge. Sum reveals kick/bass phase.
- Stereo to DigiCheck Totalyser spec: 45 deg goniometer, continuous analogue trace, dual afterglow, AGC, zoned correlation, Peak/RMS with OVR.
- Loudness meter is a NEW shared module (Pro-L 2 / EBU R128): M/S/I LUFS, True Peak, Peak, RMS, LRA, targets. Reused by the future Limiter/Clipper.
- Signal bus (cross-instance layering): same-process registry, default overlaps nothing, bottom dropdown lists discoverable channels with live previews. Ships at rollout.
- ScioSee is black-themed with each signal a distinct colour. Other plugins keep MetalPalette colours.
- All three displays ship at launch with a tabbed shell; tabs can tear off (snapped together or floating).

## Constraints and Dependencies
- Threading: DSP writes atomics/SPSC rings; 30 Hz UI timer reads; paint never touches DSP. RT-safe (no audio-thread allocation).
- Shared ScionaughLookAndFeel / ScionaughColours / MetalPalette for theming.
- Depends on the next-beta architecture/plumbing rework (signal bus lands with it).
- References: DigiCheck Totalyser, FX23 Psyscope Pro, FabFilter Pro-L 2.

## Open Questions
- D1: detach default (in-window docking vs true OS-detached windows). Recommended: in-window base + true-float opt-in.
- D2: PLUGIN_CODE 4-char (proposed ScSe).
- D3: settings exposure (currently all spectrum params user-facing).
