0408_ENGINE_MechanicsAnimationTimeSystem_v1.0.0

# 🧠 Scope

This system introduces **time-based behavior and animation** into mechanics.

It upgrades mechanics from:
- static geometry → dynamic systems
- reactive → expressive
- visual → musical

---

# ⚙️ Assumptions

- Existing physics + mechanics system
- requestAnimationFrame loop already running
- deltaTime available per frame
- Objects include:
  - ramp
  - bumper
  - field
  - emitter
  - portal

---

# 🧩 1. TIME STATE (GLOBAL)

## /engine/time.ts

```ts
export interface TimeState {
  time: number // total elapsed time
  delta: number // frame delta
}

export function updateTime(prev: TimeState, now: number): TimeState {
  const delta = (now - prev.time) / 1000

  return {
    time: now,
    delta
  }
}
```

---

# 🧩 2. ANIMATION CONFIG

## Extend BaseObject

```ts
export interface AnimationConfig {
  enabled: boolean
  type: "oscillate" | "rotate" | "pulse"
  speed: number
  amplitude: number
}

export interface AnimatedObject {
  animation?: AnimationConfig
}
```

---

# 🧠 3. ANIMATION ENGINE

## /engine/animation.ts

```ts
export function applyAnimation(obj: any, time: number) {
  if (!obj.animation?.enabled) return obj

  const { type, speed, amplitude } = obj.animation

  const t = time * speed

  switch (type) {
    case "oscillate":
      return {
        ...obj,
        offset: Math.sin(t) * amplitude
      }

    case "rotate":
      return {
        ...obj,
        rotation: t % (Math.PI * 2)
      }

    case "pulse":
      return {
        ...obj,
        strength: 1 + Math.sin(t) * amplitude
      }

    default:
      return obj
  }
}
```

---

# 🧱 4. APPLY TO MECHANICS

## Example: Field

```ts
function updateField(field: any, particle: any, time: number) {
  const animated = applyAnimation(field, time)

  const dx = animated.x - particle.x
  const dy = animated.y - particle.y

  const force = animated.strength || 1

  particle.vx += dx * force * 0.001
  particle.vy += dy * force * 0.001
}
```

---

# 🧱 5. TIMED EMITTER

```ts
export interface TimedEmitter {
  interval: number
  lastSpawn: number
}

export function updateEmitter(emitter: any, time: number) {
  if (time - emitter.lastSpawn < emitter.interval) return []

  emitter.lastSpawn = time

  return spawnParticles(emitter)
}
```

---

# 🧱 6. VELOCITY-BASED TRIGGERS

```ts
export function handleImpact(obj: any, velocity: number) {
  if (velocity > obj.threshold) {
    triggerAccent(obj.note)
  }
}
```

---

# 🌊 7. CONTINUOUS MODULATION

```ts
export function modulateFromMotion(particle: any) {
  const energy = Math.sqrt(particle.vx ** 2 + particle.vy ** 2)

  setGlobalFilter(energy * 0.1)
}
```

---

# 🎛 8. UI CONTROLS

Add:

- animation toggle
- type dropdown
- speed knob
- amplitude knob

---

# 🧠 RESULT

Mechanics now:

- move
- evolve over time
- influence sound continuously

---

# 🔥 What This Unlocks

- oscillating sound systems
- rhythmic emitters
- living environments
- generative music structures

---

# ⚡ Implementation Guide

where
- /engine/time.ts
- /engine/animation.ts

run
- integrate into main update loop

expect
- mechanics become dynamic
- sound becomes expressive
