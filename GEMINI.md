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
  drift LFO (driftBias/driftDepth). Future: right-click MIDI learn and
  per-slider LFO assign.

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
- Waveform: sine, triangle, sawtooth (per voice)
- Gate min/max: staccato–legato range
- Velocity depth: 0=flat, 1=full Barlowian dynamics (indispensability-driven)
- Echo probability: repeat last note with set probability
- Drift: LFO-like register drift (driftRate, driftDepth, driftBias)
- bassVel: relative weight for lower-octave notes

### Voices
- V1: master voice, full parameter set in P object
- V2 (voiceExts[0]): independent collapsible section, own strat/scale/etc.
- V3 (voiceExts[1]): independent collapsible section, own strat/scale/etc.
- All three share master BPM; each has own MIDI channel
- phraseLoop (L key): records 8-note phrase, replays with 80% fidelity (V1 only)

### Presets & Morph
- 8 preset slots: captureState() saves {v1, v2, v3}
- Left-click = load (A), right-click = set morph target (B)
- Manual morph fader: real-time A↔B interpolation
- Timed morph: startTimedMorph(secs) animates fader, continues from current pos
- applyMorph(): interpolates 19 numeric params, snaps discrete at t>0.5
- Note: V2/V3 params not yet interpolated by morph (V1 only)

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
- Detachable sections: float as draggable panels, reattach with ✕
- Master ⚄ randomizer in status bar; per-section ⚄ icons; R key
- Directed randomizers: each section has constrained Barlowian random logic

## What Still Needs Building
- Right-click context menu: MIDI learn, LFO assign per slider
- Score timeline visual: canvas strip with breakpoint markers and playhead
- V2/V3 params in timed morph (currently V1 only)
- Preset name shown in status bar on load
- Save/load full session to JSON file
