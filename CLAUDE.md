# BARLOWGEN — Project Brief

## What it is
Single-file browser instrument implementing Clarence Barlow's
algorithmic composition mathematics. No framework, no build step.
IBM Plex Mono font. Dark aesthetic (#03030a background, #e8c840 gold).

## Barlow mathematics
- Indigestibility ξ(N) = Σ e·2(p−1)²/p
- Harmonicity H(P:Q) = 1/(ξ(P)+ξ(Q))
- Indispensability: recursive stratification ranking
- nr = normalized rank 0–1. Strong beats nr≈1, weak nr≈0
- Velocity: 64 + velocityDepth * (127-64) * pow(nr, 0.5) − velocityDepth * 24 * (1−nr)
- Duration: pulseDur * gate * (1.4 + 1.2 * nr)
- barlowRandomStrat(): prime-weighted strat generator, P(p) ∝ 1/ξ(p)

## Architecture
- Single file: barlowgen.html (~2200+ lines)
- P object: all V1 parameters
- state object: derived from P via rebuildState()
- voiceExts[0,1]: V2 and V3, each has params+state+pTime+pIdx
- firePulse(idx, t, S, vIdx): parameterized, no globals
- scheduler(): runs every 48ms, lookahead 150ms
- vQueue: visual events keyed to audio time
- hcolor(nh, alpha, hoff): amber(38)→blue(226) harmonicity encoding
- phraseLoop: { active, len:8, buf, pos } — records/replays with 80% fidelity
- scoreAuto / tickScore(): breakpoint automation, 12 params + morph
- morphTween / startTimedMorph(secs): timed A→B morph via setInterval
- window.SCALES = SCALES: exposes scale dict to randomizers

## UI layout
- Status bar top: play | ⚄ master rand | ⊟ collapse-all | BARLOWGEN | bpm | pulses
- Right panel 280px: voice tab strip (PRIMARY · VOICE 2 · VOICE 3)
  Tab 0: RHYTHM · HARMONY · TIMBRE (PRIMARY VOICE)
  Tab 1: VOICE 2 controls
  Tab 2: VOICE 3 controls
  Below tabs always: MIDI · TUNING · SCORE · SESSION (detachable)
- State bar bottom: preset slots + morph fader + timed morph controls
- Canvas: full screen behind everything, CX = W/2

## Sections in right panel (current)
Voice tabs: PRIMARY · VOICE 2 · VOICE 3 strip at top of panel.
Tab 0 (PRIMARY): RHYTHM, HARMONY, TIMBRE
Tab 1 (VOICE 2): Voice 2 controls
Tab 2 (VOICE 3): Voice 3 controls
Always visible below tabs: MIDI OUT, TUNING, SCORE, SESSION
Each section has ▸ collapse arrow, ⓘ tooltip, ⚄ randomizer (where applicable),
⤢ detach icon. Detached sections float as draggable panels; header
stays in panel with gold dot indicator. Click header to bring float to front.

## Completed features (as of 2026-03-25, tag v1.2-working)
- ✓ Tooltip system: #tip div, data-tip on all controls, cursor-following
- ✓ Color hierarchy: gold=open header, white-gray=collapsed, near-white=values, muted-gray=labels
- ✓ Section detach: draggable floats, reattach, icon toggle ⤢/⤡, indicator dot
- ✓ Velocity depth: Barlowian reimagining — 0=flat, 1=full indispensability-driven dynamics
- ✓ V2 and V3 full controls: strat, metricity, tonality, scale, tonic, octave, ambitus, density, wave, MIDI ch
- ✓ VOICE 2 / VOICE 3 as separate collapsible sections (split from single VOICES)
- ✓ Timed morph: startTimedMorph(secs), ▶ button, morphPos persistence
- ✓ Preset system: captureState/applyState with {v1,v2,v3}, left-click=load, right-click=set B
- ✓ Directed randomizers: randomizeRhythm/Harmony/Timbre/Expression/Drift/Voice2/Voice3
- ✓ Barlowian strat generator: barlowRandomStrat(), prime weights by 1/ξ(p)
- ✓ ⚄ per-section header icons + inline expression/drift ⚄ in Timbre body
- ✓ Master ⚄ in status bar (R key also triggers)
- ✓ Score automation: 12 params automatable via breakpoint syntax
- ✓ Session save/load: full JSON snapshot (P, V2/V3 params, presets, score inputs). SESSION section at panel bottom.
- ✓ Score timeline: dark bar at canvas bottom, 12-param colored dots (amber→blue hue spread), live playhead when scoreAuto.running
- ✓ Right-click context menu: MIDI learn (range-aware CC→param mapping via onMidiMessage(), wired to all inputs via populateMidiDevices()), LFO assign per slider (sinusoidal, 3–300s period, non-destructive state write). 18 V1 sliders have data-param attributes.
- ✓ Bass shadow voice MIDI output: freqToMidi(bn.freq/2) matched to WebAudio bass, vel = round(bassVel×64) clamped 1–80, guarded by P.bassVel > 0 && voiceIdx === 0 && midiOut. Fires at same tFire and bassDur as WebAudio event.
- ✓ Preset name in status bar: #sb-title shows slot name for 3s on load, reverts to "barlowgen". clearTimeout on element prevents stacking on rapid switches. Fallback to P1…P8 if name is blank.
- ✓ V2/V3 params in timed morph: applyMorph() now interpolates 10 numeric keys (metricity, tonality, density, gate, gateMin, gateMax, octave, ambitus, velocityMin, velocityMax) and snaps discrete keys (scale, tonic, wave, midiChan, enabled) at t>0.5. strat array snapped separately. rebuildVoiceState(ve) + syncVoiceUI() called per morph tick.
- ✓ Tooltip positioning fixed: mouseenter now sets left/top before first mousemove (was rendering at viewport top-left)
- ✓ Session files use .barlo extension (accept=".barlo,.json" for backward compat with old .json saves)
- ✓ Preset buttons show slot number + truncated name (8 chars, gold, ellipsis). .pslot widened to 46px, flex-column layout.
- ✓ Preset export/import: ⬇P/⬆P buttons in state bar. exportPreset() saves presets[activeIdx] as barlowgen-<name>.barlo. importPreset() reads .barlo/.json into activeIdx, calls savePresets() + loadPreset() + renderPresets().

- ✓ Karplus-Strong synthesis: physical plucked string model per voice. playKarplus(freq, vel, dur, t, gain, kDecay, kBright, kPluck). Noise burst → delay loop → lowpass → feedback. Pitch set by delayTime = 1/freq (JI-exact). wave dropdown: sine | triangle | sawtooth | karplus. Three per-voice params in P/makeVoiceParams (karplusDecay 0.90–0.999, karplusBrightness 0.1–2.0, karplusPluck 0.0–1.0). Sliders hidden unless wave=karplus. Propagated through rebuildState/rebuildVoiceState/syncUI/syncVoiceUI.

## What still needs building
- Rename preset slot inline (double-click to edit name on button)
- Morph controls layout too tight on smaller screens — consider moving to SCORE section or popover

## Rules
- NEVER rewrite the whole file
- Always git commit before AND after every change
- One feature per prompt
- surgical str_replace edits only

## Current git state
Main branch. Tag v1.2-working = 6eb8fd2.
Post-prompt-3 state: session, timeline, ctx-menu complete.
Always check git log before editing.
Run: grep -n "function_name" barlowgen.html to find line numbers.
