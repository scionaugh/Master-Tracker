# ScioGlue — Chat Context

## Goals and Purpose
A unified four-topology compressor plugin covering Optical (LA-2A), VCA (SSL G-Bus), FET (1176), and Variable-Mu (Fairchild 670). Each topology is a hardware-derived model with physically accurate gain reduction behaviour and topology-specific Stochastic Injection. The plugin presents all four via a tabbed UI with swappable parameter panels.

## Key Decisions Made

**Four topologies, one plugin.** Model selector tabs switch the visible parameter panel; inactive panels retain their values.

**Stochastic Injection is topology-specific** — each topology has distinct physical noise sources:
- Optical: GR coefficient perturbation (CdS photoresistor carrier noise, bandlimited ~5 kHz)
- VCA: Control voltage perturbation (Johnson thermal noise in CV path, bandlimited ~12 kHz)
- FET: Pink 1/f noise in signal path (FET flicker noise, scales with signal level × GR depth)
- Variable-Mu: Both grid perturbation AND IP shot noise (same tube physics as Sciotube, scales with signal × GR depth)

**Noise only activates under compression.** At zero GR, all injection is zero regardless of Aliveness setting. This is both physically correct and perceptually appropriate — aliveness character emerges when the compressor is working.

**Optical Peak Reduction maps to threshold.** Range 0–100 (matching hardware), internally maps to `thrDb = -10 - (raw × 0.45)`. User never sees dB.

**VCA Auto Release:** When Release knob exceeds 1.2s, behaviour switches to programme-dependent. A small "AUTO" indicator appears adjacent to the value readout — discoverable without a dedicated toggle.

**FET Input (not Threshold):** `fet_input` is a drive into the signal path affecting both level and compression simultaneously, matching 1176 hardware behaviour. Attack knob UI convention matches hardware direction; DSP handles the inversion internally.

**Variable-Mu TC selector:** Six time constant options matching Fairchild 670 TC1–TC6 (0.2ms–0.3s through 0.2ms–Auto). Displayed as a ComboBox with timing info in each label.

**Auto Gain toggle:** Global across all topologies. Replaces manual makeup gain with RMS-based auto-level (300ms one-pole slew). When active, per-model gain/makeup knob is greyed out.

## Constraints and Dependencies
- `Shared/StochasticEngine.h` — SI noise system
- `SICompressor.md` — full per-topology SI calibration targets and implementation notes
- `UI_Design_V1.md` — complete layout spec including swappable panels, GR trace, meters

## Open Questions
- Browser prototype not yet built — no topology has been validated by ear
- Calibration targets in SICompressor.md are starting points only, require listening validation
- Variable-Mu tube model: implemented as a custom muSat(x, grDb) function — not a named Koren model. The Fairchild 670 gain element doesn't map cleanly to the standard Koren triode/pentode parameter set, so a custom saturation curve was used instead.
