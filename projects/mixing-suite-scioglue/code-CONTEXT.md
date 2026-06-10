# ScioGlue — Code Context
<!-- ⚠️ CONTEXT UPDATE NEEDED — Code has progressed to beta v2 with stochastic noise + UI updates. Context written before JUCE implementation was underway. -->

## Architecture Overview

```
Plugins/ScionaughCompressor/Source/
├── PluginProcessor.h/cpp   ← DSP: four topology engines, SI injection, GR metering
└── PluginEditor.h/cpp      ← UI: model tabs, swappable panels, GR trace, meters
```

SI spec: `SICompressor.md`
UI spec: `UI_Design_V1.md` (Plugin 4 — Compressor section)
Browser prototype: NOT YET BUILT

## Key Technical Decisions

**APVTS parameter IDs:**
- Global: `model`, `auto_gain`
- Optical: `opt_threshold`, `opt_attack`, `opt_cell_speed`, `opt_mode`, `opt_gain`
- VCA: `vca_threshold`, `vca_attack`, `vca_release`, `vca_ratio`, `vca_gain`
- FET: `fet_input`, `fet_attack`, `fet_release`, `fet_ratio`, `fet_gain`
- Variable-Mu: `vm_threshold`, `vm_tc`, `vm_gain`

**Stochastic Injection per topology (from SICompressor.md):**

| Topology | Mechanism | Injection Point | Noise Type |
|----------|-----------|-----------------|------------|
| Optical | CdS carrier noise | GR coefficient before application | Bandlimited white, ~5 kHz rolloff |
| VCA | CV path Johnson noise | Control voltage before VCA | Bandlimited white, ~12 kHz rolloff |
| FET | 1/f flicker noise | Signal at FET stage | Pink (1/f), use Voss-McCartney |
| Variable-Mu | Grid noise + IP shot | EG before transconductance; IP multiplicative | White bandlimited + white |

All injections zero at zero compression. Scaling forms:
- Optical: `GR_perturbed = GR_dB + noise * drive * |GR_dB|`
- VCA: `noise_amplitude = drive * ceiling * |CV_current|`
- FET: `noise_amplitude = drive * ceiling * |signal_at_FET| * GR_depth_factor`
- Var-Mu grid: `drive * ceiling_grid * |signal| * GR_depth_factor`
- Var-Mu shot: `IP_out = IP * (1 + noise * drive * sqrt(|IP_GR|) * GR_depth_factor)`

**UI components (from UI_Design_V1.md):**
- `ModelTabComponent` — 4 tabs (Optical/LA-2A, VCA/G-Bus, FET/1176, Var-Mu/Fairchild)
- Swappable panels: `OpticalPanelComponent`, `VCAPanelComponent`, `FETPanelComponent`, `VariMuPanelComponent`
- `VariMuPanelComponent` includes live ratio curve with operating point dot
- `GRTraceComponent` — scrolling GR history, newest right, AmberGlow line
- `LevelMeterComponent` × 2 (IN, OUT)
- `juce::ToggleButton` — Auto Gain

**Threading:** `currentGrDb` atomic written by DSP thread, read by UI timer at 30Hz for GR trace and Var-Mu ratio curve dot.

## Dependencies on Other Projects
- `Shared/StochasticEngine.h` — SI noise system
- `Shared/ScionaughLookAndFeel.h/cpp` — all UI styling
- `SICompressor.md` — complete SI implementation spec

## Known Issues / Technical Debt
- No browser prototype — no topology validated
- Variable-Mu tube model parameters not specified in SICompressor.md (which tube?)
- Pink noise generator (Voss-McCartney) not yet implemented in Shared/

## Open Questions
- Which tube model parameters for the Variable-Mu gain reduction element?
- Build browser prototype first or go direct to JUCE?
- GR depth factor normalisation: 0..1 linear vs logarithmic?
