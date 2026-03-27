# BARLOWGEN: Core Philosophy & Roadmap

## Philosophy: Organic Mathematics
Barlowgen is a realization of Clarence Barlow's theories on Metricity,
Tonality, and Indispensability. The goal is to move beyond static
computer-generated MIDI into a "living" mathematical engine where
qualitative musical traits are quantified and modulated in real-time.

## Foundational Mandates
- **Indispensability-Driven Logic:** All temporal and harmonic variations
  (jitter, probability, picking) must be weighted by Barlow's indispensability
  ranks. Stronger beats provide the anchor; weaker beats provide the "life"
  and drift. barlowRandomStrat() uses 1/ξ(p) weighting so simpler primes
  (p=2) appear most often in generated meters.
- **Just Intonation (JI) Integrity:** The engine remains faithful to JI ratios.
  Microtonality is a feature, not a bug. MPE/Pitch-bend preserves these
  deviations when communicating via WebMIDI.
- **Parameter Modulation:** Parameters should be interactive and discoverable.
  Currently: breakpoint score automation, timed A→B morph, phraseLoop,
  drift LFO (driftBias/driftDepth), right-click MIDI learn and per-slider
  LFO assign (sinusoidal, non-destructive state write).

## Implemented (as of 2026-03-25, tag v1.2-working)

### Rhythm
- Stratification: user-editable, barlowRandomStrat() for weighted generation
- Metricity: gates note probability by indispensability rank
- Density: overall note density, independent of metricity
- Jitter: micro-timing offsets weighted by pulse rank
- Swing: late-beat offset scaled by nr
- Rest clustering: probability of rest following rest
- Metric rotation: shifts downbeat phase within stratification cycle

### Harmony
- Scales: just intonation major/minor, pentatonic, pythagorean, modes,
  Indian ragas (directional aroha/avaroha), hirajoshi, insen, octatonic
- Tonality: weights note selection by harmonicity H(P:Q)
- Complexity: filters pool by harmonicity floor
- Tonic shift: real-time pitch bend offset
- Synnefism: blends harmonicity weighting toward uniform

### Timbre & Expression
- Waveform: sine, triangle, sawtooth, karplus (per voice)
- Karplus-Strong: physical plucked string — noise burst → feedback delay → lowpass. delayTime = 1/freq (JI-exact). playKarplus(freq, vel, dur, t, gain, kDecay, kBright, kPluck). Per-voice params: karplusDecay (decay rate), karplusBrightness (lowpass cutoff ×freq), karplusPluck (burst length 1–5 cycles). Sliders revealed only when wave=karplus.
- Gate min/max: staccato–legato range
- Velocity depth: 0=flat, 1=full Barlowian dynamics (indispensability-driven)
- Echo probability: repeat last note with set probability
- Drift: LFO-like register drift (driftRate, driftDepth, driftBias)
- bassVel: relative weight for lower-octave notes; bass shadow voice now routed to MIDI output (same channel as V1, velocity scaled by bassVel, JI-accurate via freqToMidi)

### Voices
- V1: PRIMARY VOICE, full parameter set in P object
- V2 (voiceExts[0]): independent collapsible section, own strat/scale/etc.
- V3 (voiceExts[1]): independent collapsible section, own strat/scale/etc.
- All three share master BPM; each has own MIDI channel
- phraseLoop (L key): records 8-note phrase, replays with 80% fidelity (V1 only)

### Presets & Morph
- 8 preset slots: captureState() saves {v1, v2, v3}
- Left-click = load (A), right-click = set morph target (B)
- Preset buttons show slot number + name (truncated at 8 chars, gold)
- Individual preset export/import via .barlo files (⬇P/⬆P in state bar)
- Manual morph fader: real-time A↔B interpolation
- Timed morph: startTimedMorph(secs) animates fader, continues from current pos
- applyMorph(): interpolates 19 numeric V1 params + 10 numeric V2/V3 params, snaps discrete at t>0.5
- V2/V3 params fully interpolated: 10 numeric keys, 4 discrete snap at t>0.5, strat array snapped
- Session files use .barlo extension (accepts .barlo and .json for backward compat)

### Score Automation
- Breakpoint syntax: "0:0.65 30:0.9 60:0.3" (time in seconds)
- 12 automatable params: metricity, tonality, density, bpm, volume, gate,
  jitter, swing, restCluster, echoProbability, driftDepth, velocityDepth
- Morph position (morph A↔B) also automatable

### UI
- Dark aesthetic: #03030a background, #e8c840 gold accent
- IBM Plex Mono throughout
- Tooltip system: data-tip on all controls, cursor-following #tip div
- Section icons (ⓘ ⚄ ⤢) gold-tinted at rest, bright on hover
- Voice tab strip at top of right panel: PRIMARY · VOICE 2 · VOICE 3
  Tab 0 (PRIMARY): RHYTHM · HARMONY · TIMBRE
  Tab 1: VOICE 2 controls
  Tab 2: VOICE 3 controls
  Always visible below: MIDI · TUNING · SCORE · SESSION
- Detachable sections: float as draggable panels, reattach with ✕
- Master ⚄ randomizer in status bar; per-section ⚄ icons; R key
- Directed randomizers: each section has constrained Barlowian random logic
- Session save/load: JSON snapshot of P, V2/V3 params, presets, score inputs (SESSION section)
- Score timeline: per-param colored breakpoint markers (amber→blue hue spread), live playhead (scoreAuto.running)
- Right-click context menu: MIDI learn + LFO assign per slider (18 V1 sliders have data-param)
- Preset name flash in status bar on load (3s, auto-revert)

## What Still Needs Building
Original roadmap complete as of 2026-03-25.
