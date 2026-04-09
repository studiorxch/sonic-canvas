0408_ENGINE_MIDISpatialSequencing_v1.0.0

# 🧠 Scope

This system introduces **MIDI mapping + spatial sequencing**.

It transforms the canvas into a **playable sequencer**, where:

- position = timing
- color = pitch
- motion = performance

---

# ⚙️ Assumptions

- Existing:
  - Particle system
  - Audio trigger system
  - Instrument + note mapping
  - Mechanics + collisions
- Canvas uses normalized coordinate space

---

# 🧩 1. SPATIAL TIMELINE MODEL

## /types/sequencer.ts

```ts
export interface SpatialTimeline {
  axis: "x" | "y"
  length: number
  bpm: number
  bars: number
}
```

---

# 🧠 2. POSITION → TIME MAPPING

## /engine/spatialTime.ts

```ts
export function positionToTime(
  position: number,
  timelineLength: number,
  totalTime: number
): number {
  const normalized = position / timelineLength
  return normalized * totalTime
}
```

---

# 🧠 3. TIME → STEP (QUANTIZATION)

```ts
export function quantizeTime(
  time: number,
  bpm: number,
  division: number
): number {
  const beatDuration = 60 / bpm
  const step = beatDuration / division

  return Math.round(time / step) * step
}
```

---

# 🧩 4. MIDI NOTE MAPPING

## /engine/midiMap.ts

```ts
const NOTE_MAP: Record<string, number> = {
  C: 0, "C#": 1, D: 2, "D#": 3,
  E: 4, F: 5, "F#": 6, G: 7,
  "G#": 8, A: 9, "A#": 10, B: 11
}

export function noteToFrequency(note: string, octave: number): number {
  const n = NOTE_MAP[note] + (octave + 1) * 12
  return 440 * Math.pow(2, (n - 69) / 12)
}
```

---

# 🧱 5. COLOR → NOTE SYSTEM

```ts
export const COLOR_NOTE_MAP = {
  red: "C",
  orange: "D",
  yellow: "E",
  green: "F",
  cyan: "G",
  blue: "A",
  purple: "B"
}
```

---

# 🧱 6. SPATIAL TRIGGER

```ts
export function handleSpatialTrigger(obj: any, particle: any, timeline: any) {
  const time = positionToTime(
    particle.x,
    timeline.length,
    timeline.bars * 4 * (60 / timeline.bpm)
  )

  const quantized = quantizeTime(time, timeline.bpm, 4)

  scheduleNote(obj.note, quantized)
}
```

---

# 🧱 7. SCHEDULER

## /engine/scheduler.ts

```ts
export function scheduleNote(note: string, time: number) {
  const now = audioContext.currentTime
  const delay = Math.max(0, time - now)

  setTimeout(() => {
    triggerSound(note)
  }, delay * 1000)
}
```

---

# 🌊 8. PARTICLE AS PLAYHEAD

```ts
export function updatePlayhead(particle: any, timeline: any) {
  const progress = particle.x / timeline.length

  particle.playhead = progress
}
```

---

# 🎛 9. UI CONTROLS

Add:

- Timeline axis (X / Y)
- BPM (already exists)
- Quantize division
- Grid overlay (visual timing)

---

# 🧠 RESULT

Canvas becomes:

- a sequencer
- a timeline
- a performance space

---

# 🔥 What This Unlocks

- draw beats spatially
- particles act as moving playheads
- visual composition = musical composition
- import MIDI → place notes spatially

---

# ⚡ Implementation Guide

where
- /engine/spatialTime.ts
- /engine/midiMap.ts
- /engine/scheduler.ts

run
- integrate into collision + trigger system

expect
- canvas behaves like sequencer
- timing becomes visible + controllable
