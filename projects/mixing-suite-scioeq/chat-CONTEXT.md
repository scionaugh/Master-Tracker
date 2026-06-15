# ScioEQ — Chat Context

<!-- Only read when asked for deeper evaluation or cross-project analysis -->

## Goals and Purpose
Parametric EQ for the Scionaugh Mixing Suite, built on the suite's verified Zavalishin/Simper ZDF trapezoidal SVF. Cold, surgical character (icy blue-white palette) to contrast the warmer EQP. Provides the shared spectral/filter front end and SVF that ScioEQP and ScioMB reuse.

## Key Decisions Made
- ZDF SVF (SVFBand.h) as the filter core; G_CLAMP 0.9999, W_CLAMP prewarp clamp.
- UI v2 adds AbstractFifo input+output spectrum analyser, resonance detection layer, M/S gradient EQ curve.
- Linear Phase toggle currently only gates Aliveness (no FIR path ships yet — noted during ScioMB research).

## Constraints and Dependencies
- Shared ScionaughLookAndFeel (Cinzel + Special Elite). Palette is the per-plugin token swap.
- Is the upstream source for ScioMB's crossover SVF and ScioEQP's filter math.

## Open Questions
- Parameter tuning: which zones are unusable; whether to apply 15%-extreme-zone parametric mapping suite-wide.
