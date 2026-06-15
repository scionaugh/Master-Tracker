# ScioEQ — Code Context

<!-- Only read when asked for deeper evaluation or cross-project analysis -->

## Architecture Overview
JUCE plugin. ZDF trapezoidal SVF per band (SVFBand.h), APVTS with string-slug enums and log NormalisableRange. AbstractFifo FFT feed for the spectrum analyser. Stochastic (Aliveness) g-drift on the SVF coefficients.

## Key Technical Decisions
- SVF chosen over Direct Form for low-frequency and modulated-coefficient numerical precision (SVF-vs-DF1 table).
- g-drift Aliveness with headroom scaling (hs = min(1,(G_CLAMP-gNom)/MAX_DRIFT)); bypassed when kernels would be static.
- M/S convention m=0.5(l+r), s=0.5(l-r) — the suite reference convention.

## Dependencies on Other Projects
- SVFBand.h is ported by ScioMB (crossover) and informs ScioEQP.
- Shared StochasticEngine.h, ScionaughLookAndFeel.

## Known Issues / Technical Debt
- No FIR/linear-phase path actually ships; the Linear Phase toggle only gates Aliveness.

## Open Questions
- Parameter tuning pass outcomes (unusable zones, extreme-zone mapping).
