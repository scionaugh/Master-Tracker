# ScioSee - Code Context

<!-- Only read when asked for deeper evaluation or cross-project analysis -->

## Architecture Overview
New plugin target `ScionaughAnalyser` (product ScioSee), plus a new shared display layer:
- `Plugins/Shared/Analysis/` : SpectrumAnalyzer, ScopeBuffer, StereoAnalyzer, LoudnessMeter, SignalBus (DSP-thread producers, plain snapshot structs, no JUCE Graphics).
- `Plugins/Shared/Visualization/` : SpectrumDisplay, ScopeDisplay, StereoDisplay, LoudnessDisplay (juce::Component renderers, 30 Hz, read colour from LookAndFeel).

## Extraction Sources (verified on next-beta)
- SpectrumAnalyzer/Display: ScioEQ + ScioMB AbstractFifo spectrum feed (specFifoIn/Out, order 13 = 8192), UI drains at 30 Hz.
- ScopeBuffer/Display: ScioGlue SPSC waveform rings (kOutScopeLen = 4096, output + sidechain), playhead BPM, 1/4-2 bar window.
- StereoAnalyzer/Display: ScioSpace VectorscopeComponent (Lissajous, scopeL/scopeR, kScopeLen), CorrelationMeterComponent, correlationValue atomic.
- LoudnessMeter/Display: new (ITU-R BS.1770 / EBU R128).
- SignalBus: new; reuses the per-process static pattern in ScionaughSignalProbe (liveInstances()).

## Key Technical Decisions
- Spectrum Backend A (FFT-binned thirds) in EQ/EQP/MB; Backend B (true 1/3-octave filterbank) in ScioSee; same band struct, shared display, calibration parity (sine sweep + pink noise acceptance test, RMS sine +3 dB option).
- Scope: SPSC rings per tap; layers (enable/reorder/rename/colour/invert/latency-nudge); sum + per-column interference metric; beat-sync vs free Hz/note; segment analysis (peak/ms/samples/Hz/note).
- Stereo: 45 deg mapping (mid vertical, side horizontal), continuous trace, dual afterglow, AGC, correlation, peak/RMS.
- Loudness: K-weighting, Momentary 400 ms / Short-term 3 s / Integrated (gated) LUFS, LRA, 4x true peak, peak, RMS, targets -9/-14/-23/-24.
- SignalBus: process-static registry, double-buffer/seqlock snapshot publish, discovery with preview thumbnails, lifecycle age-out, same-process only, versioned snapshot format.

## Build
- CMake target ScionaughAnalyser following the suite pattern. PRODUCT_NAME "ScioSee", COMPANY_NAME "Scionaugh", PLUGIN_MANUFACTURER_CODE Scio, PLUGIN_CODE ScSe (proposed), BUNDLE_ID com.scionaugh.analyser. VST3 + AU.

## Spec + Prompts
- `Plugins/ScionaughAnalyser/Design_Scope.md`, `DSP_Notes.md`, `UI_Notes.md`, `ScioSee_CodePrompts.md` (7-phase extract-first build, Opus 4.8, per-phase verify + final subagent audit).

## Status
Design done; spec + code prompts written; no code yet.
