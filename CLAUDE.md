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
- Velocity: velocityMin + Math.pow(nr, 0.5) * range
- Duration: pulseDur * gate * (1.4 + 1.2 * nr)

## Architecture
- Single file: barlowgen.html (~current line count)
- P object: all V1 parameters
- state object: derived from P via rebuildState()
- voiceExts[0,1]: V2 and V3, each has params+state+pTime+pIdx
- firePulse(idx, t, S, vIdx): parameterized, no globals
- scheduler(): runs every 48ms, lookahead 150ms
- vQueue: visual events keyed to audio time
- hcolor(nh, alpha, hoff): amber(38)→blue(226) harmonicity encoding

## UI layout
- Status bar top: play | BARLOWGEN | bpm | pulses
- Right panel 280px: fixed, scrollable, collapsible sections
- State bar bottom: preset slots + morph fader
- Canvas: full screen behind everything, CX = W/2

## Sections in right panel
RHYTHM · V1, HARMONY · V1, VOICES, MIDI OUT, TIMBRE · V1, TUNING
Each has ► collapse arrow. Each has detach icon to pop out as
draggable floating card. Global collapse-all icon at panel top.

## Rules
- NEVER rewrite the whole file
- Always git commit before AND after every change
- One feature per prompt
- surgical str_replace edits only

## Current git state
Main branch. Always check git log before editing.
Run: grep -n "function_name" barlowgen.html to find line numbers.
