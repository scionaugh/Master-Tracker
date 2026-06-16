# ScioGlue — Compressor

**Last Updated:** 2026-06-16
**Status:** ACTIVE — Beta v2 installed, awaiting testing; numeric GR readout added (Phase G, 1538a80, 2026-06-15)

## Current Focus
VST beta v2 created and installed with stochastic noise implementation and UI updates. Awaiting testing.

## Next Actions
- Test stochastic noise behaviour at varying Aliveness settings
- Test UI updates
- Log bugs and findings for next development cycle

## Blockers
<!-- Leave blank if none -->







## Recent Commits
- 2026-06-16 · 1fddfbb · feat(compressor): sidechain HPF + listen monitor
- 2026-06-16 · 444ee60 · feat(compressor): external sidechain input bus + detection routing
- 2026-06-16 · 9d1760f · fix(compressor): apply attack once (instant-peak detector + single gain pole) so the ms knob is accurate
- 2026-06-16 · eb4c666 · docs(compressor): spec attack-fix + external sidechain + diode-bridge; add ScioGlue test scaffold
- 2026-06-15 · 2b118f0 · refactor(telemetry): session snapshots + delta send + retention cap + sendEvent foundation
