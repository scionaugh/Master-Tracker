# ScioGlue — Code Context

## Architecture Overview

```
Plugins/ScionaughCompressor/Source/
├── PluginProcessor.h/cpp   ← Four topology engines, SI, GR metering, BPM sync, presets
└── PluginEditor.h/cpp      ← Model tabs, swappable panels, GR trace, meters
```

## Current Implementation (beta v2)

**Four topology engines, each a separate method:**
- `processOptical()` — LA-2A: EL panel + CdS photoresistor model, cell speed (Fast/Classic/Slow)
- `processVCA()` — SSL G-Bus: CV-based gain reduction, programme-dependent auto release
- `processFET()` — 1176: feedback topology, input drive, ratio creep accumulator
- `processVariMu()` — Fairchild 670: tube transconductance model with `muSat()` nonlinearity

**Variable-Mu tube saturation:** `muSat(x, grDb)` — static nonlinearity on the gain reduction element. The Variable-Mu tube question from the design docs (which tube?) is answered in the code — a custom `muSat()` function rather than a named Koren model, reflecting the Fairchild's distinct tube topology.

**Oversampling:** Off / 2x / 4x. Applied to the WHOLE engine (detector + gain + saturation), not just the nonlinearity — ensures attack/release ballistics stay wall-clock accurate at all OS settings.

**Fairchild 670 TC table:**
| TC | Attack | Release | Auto |
|----|--------|---------|------|
| TC1 | 0.2ms | 0.3s | No |
| TC2 | 0.2ms | 0.8s | No |
| TC3 | 0.4ms | 2.0s | No |
| TC4 | 0.4ms | 5.0s | No |
| TC5 | 0.4ms | 0.2s | Yes |
| TC6 | 0.2ms | 0.3s | Yes |

**Optical cell-speed table:**
| Speed | Fast Rel | Slow Rel | Atk Scale |
|-------|----------|----------|-----------|
| Fast | 30ms | 0.40s | 0.7x |
| Classic | 60ms | 1.20s | 1.0x |
| Slow | 90ms | 3.50s | 1.5x |

**Stochastic Injection — all five generators active:**
- opticalNoise[2]: `BandlimitedWhiteNoise`, 5 kHz, kCeilingOptical = 0.003f
- vcaNoise[2]: `BandlimitedWhiteNoise`, 12 kHz, kCeilingVCA = 0.001f
- fetNoise[2]: `PinkNoise` (Voss-McCartney), kCeilingFet = 0.0003f
- vmuGridNoise[2]: `BandlimitedWhiteNoise`, 8 kHz, kCeilingVmGrid = 0.003f
- vmuShotNoise[2]: `WhiteNoise`, kCeilingVmShot = 0.0005f
- All scale with GR depth — zero injection at zero compression

**Auto gain:** Smoothed linear makeup matching output RMS to input RMS. Replaces manual makeup when toggled. Model-agnostic.

**GR metering:** `currentGrDb` atomic. `currentVmOverDb` for Variable-Mu ratio curve live dot.

**BPM-sync:** `hostBpm`, `hostTsNum`, `hostTsDen` atomics read from playhead each block. GR display window length user-selectable (1/4, 1/2, 1, 2 bars).

**Output waveform ring buffer:** 4096 samples, mono (0.5*(L+R)), feeds GR display half-wave overlay.

**Parameters (APVTS IDs):** `model`, `auto_gain`, `oversample`, `aliveness` + per-topology params as per UI_Design_V1.md.

**11 factory presets.**

## Dependencies
- `Shared/StochasticEngine.h`
- `Shared/ScionaughLookAndFeel.h/cpp`
- `Shared/ScionaughTelemetry.h/cpp`

## Open Questions
- Testing outcomes from beta v2 pending
- muSat() characterisation: custom function, not a named Koren model. How closely does it reflect the Fairchild 670 tube's actual transconductance curve?
- DspState resetted on model switch — is state preserved correctly when switching back?
