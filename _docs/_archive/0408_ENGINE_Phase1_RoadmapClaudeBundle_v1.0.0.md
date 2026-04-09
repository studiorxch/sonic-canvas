0408_ENGINE_Phase1_RoadmapClaudeBundle_v1.0.0

# 🧠 PURPOSE

This document is the **execution bundle for Claude**.

It combines:
- Phase 1 roadmap checklist
- strict implementation rules
- exact scope boundaries

Goal:
Deliver a **stable, predictable engine** before expanding features.

---

# 🎯 PHASE 1 CHECKLIST

## 🔲 Selection System
- [ ] Single select works
- [ ] Multi-select works
- [ ] Shared properties computed correctly
- [ ] Conflicting values show blank/placeholder
- [ ] Batch updates apply to all selected
- [ ] No UI desync from state

---

## 🔲 Audio Mode System
- [ ] audioMode exists on all objects
- [ ] Modes:
  - [ ] trigger
  - [ ] modulate
  - [ ] silent
- [ ] Default assignment per type:
  - ramp → silent
  - field → modulate
  - bumper/emitter/portal → trigger
- [ ] Trigger system respects audioMode
- [ ] Modulation system respects audioMode
- [ ] Silent objects produce no sound

---

## 🔲 Mechanics (Locked Set)
- [ ] ramp
- [ ] bumper (hard)
- [ ] bumper (elastic)
- [ ] field
- [ ] absorber
- [ ] emitter
- [ ] portal

Rules:
- [ ] No new mechanics introduced
- [ ] Each mechanic uses explicit properties (no hidden logic)

---

## 🔲 Loop System
- [ ] Emitter produces particles consistently
- [ ] Portal correctly links via targetId
- [ ] Portal preserves velocity
- [ ] Absorber removes safely (no errors)
- [ ] System can run indefinitely
- [ ] No runaway particle growth

---

## 🔲 Audio Trigger Layer
- [ ] Collision triggers work
- [ ] Spawn triggers work
- [ ] Field uses modulation ONLY (no trigger)
- [ ] Velocity → volume mapping applied
- [ ] No duplicate triggers per frame

---

## 🔲 World System (Minimal)
- [ ] Mode = "gravity" only
- [ ] Direction vector applied
- [ ] Strength applied consistently
- [ ] No additional modes introduced

---

## 🔲 UI Binding
- [ ] Left panel: tools + mechanics
- [ ] Center: canvas
- [ ] Right panel: inspector

Inspector must support:
- [ ] transform (x, y, rotation, scale)
- [ ] behavior (type, strength)
- [ ] audioMode
- [ ] emitter settings (rate, speed, spread)

---

# ❌ EXCLUDED SYSTEMS (DO NOT BUILD)

- animation system
- time system
- MIDI / sequencing
- ADSR / advanced audio
- scene export/import
- world modes beyond gravity

---

# 🧱 DATA MODEL (FINAL)

```ts
type EngineObject = {
  id: string
  type: "ramp" | "bumper" | "field" | "absorber" | "emitter" | "portal"

  x: number
  y: number
  rotation: number
  scale: number

  strength?: number
  targetId?: string

  audioMode: "trigger" | "modulate" | "silent"
  note?: string

  rate?: number
  speed?: number
  spread?: number
}
```

---

# 🧠 CLAUDE DIRECTIVE (COPY THIS)

Refactor and stabilize the existing engine using ONLY the Phase 1 specification.

Constraints:
- Do NOT introduce new systems
- Do NOT add new mechanics
- Do NOT add animation, time, or MIDI features
- Ensure UI always reflects actual state
- Ensure all behavior is explicit and data-driven
- Ensure system runs indefinitely without instability

Focus on:
- correctness
- consistency
- clarity of interaction

---

# 🔒 IMPLEMENTATION RULES

1. No hidden state  
2. No implicit behavior  
3. No duplicated logic  
4. All updates go through state (no mutation side-effects)  
5. Clamp unsafe values (velocity, spawn rate, etc.)  

---

# 🧪 VALIDATION TESTS

## Loop Test
- Build emitter → ramp → bumper → portal
- System runs 5+ minutes without failure

## Selection Test
- Select 1 object → edit works
- Select 5 objects → batch edit works
- Mixed types → conflicting fields blank

## Audio Test
- Ramp → no sound
- Field → modulation only
- Bumper → trigger sound
- Velocity affects volume

---

# ⚡ IMPLEMENTATION GUIDE

where
- full engine refactor pass

run
- apply checklist sequentially (selection → audio → mechanics → UI)

expect
- stable system
- predictable behavior
- clean interaction model

---

NEXT PHASE (NOT INCLUDED):
- animation
- time system
- MIDI sequencing
