# Sound Science Course — Chat Context

<!-- Only read when asked for deeper evaluation or cross-project analysis -->

## Goals and Purpose
An 18-lesson course teaching the maths and physics underpinning audio engineering — waves, Fourier analysis, logarithms/dB, calculus (differentiation/integration as filters), complex numbers, the wave equation, sampling/quantisation, phase, op-amps/saturation, convolution, stereo width, control systems, modulation, and psychoacoustics. Designed for dual delivery: as LMS course content and as YouTube videos (each lesson has a dedicated "hook angle" and target length). Source: `Sound_Science_Curriculum.xlsx`.

## Course Structure (18 Lessons)
1. Single Waves & Signals — sine waves, amplitude/frequency/phase
2. Combining Waveforms — superposition, phase cancellation
3. Frequency Decomposition & Fourier — spectrum analysis, timbre
4. Logarithms & Decibels — dB scale, gain staging
5. Differentiation — transients & high-pass filtering
6. Integration — smoothing & low-pass filtering, RMS vs peak
7. Envelope vs. Frequency — why a sub kick still punches
8. Taylor Series & Harmonic Synthesis — additive synthesis
9. Complex Numbers & The Unit Circle — phase, Euler's formula
10. The Wave Equation — standing waves, room modes
11. Signal Digitisation — sampling, Nyquist, quantisation, bit depth
12. Phase Distortion of Tools — filter phase response, linear vs minimum phase
13. Amplification & Op-Amps — gain, feedback, harmonic distortion/saturation
14. Convolution & Impulse Responses — convolution reverb
15. Stereo Width & Mono Compatibility — mid-side as vector decomposition
16. Control Systems & Compressors — compressors as feedback control systems
17. Symmetries, Trig Identities & Modulation — ring mod, FM synthesis
18. Psychoacoustics — Perception vs. Physics — equal-loudness, masking

## Key Decisions Made
- Each lesson follows a fixed template: Core Math Concept, Audio Application, Prerequisites (which prior lessons are needed), Learning Objectives, YouTube Hook Angle + target length, Key Analogies/Metaphors, a Non-Audio Real-World Analogy, an Interactive Activity (often Desmos or DAW-based), a Deep Dive with worked examples/problems, and an Advanced Extension into CS/physics/quantum mechanics.
- Lessons are sequenced with explicit prerequisites — e.g. Lesson 9 (Complex Numbers) depends on Lessons 1–8; Lesson 16 (Compressors) depends on Lessons 5–6 and 13.
- Designed to connect directly to plugin/DSP work elsewhere in the project — several lessons map onto concepts already implemented in the Mixing Suite (e.g. Lesson 13's tanh soft-clipping ↔ ScioGlue/Sciotube saturation, Lesson 15's mid-side decomposition ↔ ScioSpace, Lesson 16's compressor-as-control-system ↔ ScioGlue topologies).

## Constraints and Dependencies
- Delivered via the ScioSound LMS platform (`../lms`), which currently has only example modules — this course's content has not been implemented in the LMS yet.
- Dual-format requirement (LMS + YouTube) — each lesson's scope/length needs to work for both.
- Possible companion relationship to the Ableton course (`../lms-ableton-course`) — several Ableton "Nerd Section" topics overlap with Sound Science lessons (e.g. Ableton M1 harmonic series ↔ Sound Science Lesson 3 Fourier; Ableton M3 phase/interference ↔ Sound Science Lesson 2).

## Open Questions
- Build order/priority for the 18 lessons — sequential by prerequisite chain, or prioritised by which support current Mixing Suite/marketing needs?
- How do the LMS version and YouTube version of each lesson differ in scope/format?
- Relationship to the Ableton course: standalone, prerequisite, or cross-referenced companion content?
