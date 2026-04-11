0410_WALL_OF_SOUND_NoteSystemUpgrade_v1.0.0

Generated: 04/10/2026

---

## 🎯 Objective

Upgrade the note system from a limited 12-note (single octave) model to a full-range MIDI system that supports:

- multiple octaves (3–4 minimum)
- bass + melody + percussion separation
- scalable musical complexity
- minimal UI overhead

---

## ⚠️ Current Limitation

System currently uses:

```js
noteClass (0–11)
```

This results in:

- single octave output
- no bass/melody separation
- limited harmonic range
- reduced musical expressiveness

---

## ✅ Required Behavior

### 1. Full MIDI Note Support

System must support:

```js
noteNumber (0–127)
```

All sound output must use `noteNumber` instead of `noteClass`.

---

### 2. Octave Control

Introduce octave offset:

```js
state.octave = 3; // default
```

Compute final note:

```js
const noteNumber = state.octave * 12 + noteClass;
```

---

### 3. Segment-Level Storage

Each segment must store its own note:

```js
segment.noteNumber = noteNumber;
```

NOT:

```js
segment.noteClass;
```

---

### 4. Backward Compatibility

If existing segments only have `noteClass`, convert:

```js
segment.noteNumber = 3 * 12 + segment.noteClass;
```

---

## 🔧 Implementation Requirements

### A. Data Model Update

Update Segment:

```js
type Segment = {
  id: string;
  ...
  noteNumber: number; // NEW
}
```

Remove reliance on:

```js
noteClass;
```

---

### B. MIDI Output Update

Replace:

```js
midiOutput.send([0x90, noteClass, velocity]);
```

With:

```js
midiOutput.send([0x90, noteNumber, velocity]);
```

---

### C. Inspector UI (Minimal)

Add octave control:

```html
<input id="octave" type="range" min="1" max="7" step="1" value="3" />
```

---

### D. Optional: Scale Mode (Recommended)

Add:

```js
state.noteMode = "chromatic"; // or "scale"
```

Example scale:

```js
const scale = [0, 2, 4, 7, 9]; // pentatonic
```

Mapping:

```js
const noteNumber = baseOctave * 12 + scale[noteIndex];
```

---

### E. Optional: Advanced Mode Toggle

UI toggle:

```txt
[ ADVANCED ▸ ]
```

Reveals:

- Octave control
- Scale selection
- Velocity
- MIDI channel

---

## 🎹 Resulting Capabilities

| Function               | Enabled |
| ---------------------- | ------- |
| Basslines              | ✅      |
| Melody                 | ✅      |
| Chords                 | ✅      |
| Multi-octave patterns  | ✅      |
| Zone-based composition | ✅      |

---

## 🧠 Optional Enhancements

### A. Spatial Mapping

```js
if (y < 400) noteNumber += 24; // higher pitch
if (y > 1200) noteNumber -= 12; // bass
```

---

### B. Role-Based Assignment

```js
segment.role = "bass" | "melody" | "perc";
```

---

## 🚫 Constraints

- DO NOT break existing scenes
- DO NOT remove current note UI
- DO NOT introduce complex UI
- DO NOT change timing system

---

## ✅ Deliverables

Claude must return:

1. Updated data model
2. Updated MIDI output logic
3. Inspector UI addition (octave)
4. Backward compatibility logic

No pseudocode. Fully working code only.

---

## 🧪 Success Criteria

- System outputs across multiple octaves
- Bass + melody separation is possible
- Existing scenes still function
- MIDI output uses full note range

---

## ⚡ Implementation Guide

- where: segment model, inspector UI, midiOut.js
- run: assign shapes across different octaves
- expect: full musical range + improved composition depth
