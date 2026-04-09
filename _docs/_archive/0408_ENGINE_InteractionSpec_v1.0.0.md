# 0408_ENGINE_InteractionSpec_v1.0.0
Date: 04-08

---

## Assumptions

- Input: mouse + keyboard
- Render: Canvas
- Engine: physics + audio loop running per frame
- Selection model: single + multi

---

## 1. Object Creation Rules

### Particle

User selects Particle tool → clicks canvas

→ Create ParticleSystem
→ Assign default:
  - instrumentId: first available
  - note: "C1"
→ Set position to click
→ Auto-select
→ Open floating particle panel

---

### Shape

User selects shape tool → drag or click

→ Create Shape
→ No physics interaction
→ Selectable immediately

---

### Mechanic

User selects mechanic (Ramp, Bumper, etc.)

→ Create Mechanic
→ Immediately participates in physics
→ Auto-select
→ Appears in Object panel

---

## 2. Selection Model

Click:
→ selects single object

Shift + Click:
→ multi-select

Drag:
→ move selected objects

Selection updates:
→ Object panel reflects current selection
→ Multiple selection disables conflicting controls

---

## 3. Physics Rules

### Update Loop (per frame)

1. Apply world force
2. Update velocity
3. Move particle
4. Check collisions
5. Resolve interactions

---

### Gravity

world.mode = 'gravity'

→ velocity.y += strength (direction applied)

---

### Ramp

→ redirects velocity along surface angle

---

### Bumper (Hard)

→ reflects velocity vector

---

### Bumper (Elastic)

→ reflects + adds energy (velocity multiplier)

---

### Field

→ applies continuous force inside region

---

### Absorber

→ removes particle from system

---

### Portal

→ teleport particle to linked portal
→ preserve velocity

---

### Emitter

→ spawns particles at interval
→ uses ParticleSystem config

---

## 4. Audio Trigger Rules

### Trigger Types

Collision-based:
→ Bumper, Ramp

Continuous:
→ Field

Spawn-based:
→ Emitter

Teleport:
→ Portal (optional)

---

### Execution

On trigger:

if audioMode !== 'off':
  → lookup instrument
  → play note

---

## 5. Loop System

Minimum viable loop:

Emitter → spawns particles  
Gravity → moves particles  
Mechanics → redirect  
Portal → returns particles  

Result:
→ continuous motion
→ continuous sound

---

## 6. UI → Data Binding

### Instrument Panel

Note Grid:
→ particle.note

Instrument Dropdown:
→ particle.instrumentId

Knobs:
→ instrument.volume
→ instrument.pitch
→ instrument.quantize

---

### Behavior Panel

Dropdown:
→ behavior.type

Strength:
→ behavior.strength

---

### Transform Panel

Scale:
→ shape.scale

Rotate:
→ shape.rotation

---

### World Panel

Mode:
→ world.mode

Direction:
→ world.direction

Strength:
→ world.strength

---

### Particle Panel

Size:
→ particle.size

Count:
→ particle.count

Speed:
→ particle.speed

Spread:
→ particle.spread

---

## 7. Expected System Behavior

- Particles move continuously
- Mechanics alter motion predictably
- Audio triggers consistently
- Loops sustain indefinitely

---

## Implementation Guide

- where: `/engine/interactionSpec.md`
- run: align physics + UI handlers to rules
- expect: consistent behavior across UI and engine
