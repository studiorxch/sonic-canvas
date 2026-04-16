# 0414_WALL_OF_SOUND_FlipperAndParticleCollision_v1.0.0

## 🎯 Objective

Add core gameplay interaction to the system by introducing:

- Flipper (rotational impulse surface)
- Particle ↔ Particle (ball ↔ ball) collision

This enables:
- Breakout / pinball-style interaction
- chain reactions
- collision-based sound generation

DO NOT modify:
- shape system
- lifecycle system
- sampler system

---

## 🧠 Assumptions

- Balls (particles) already exist
- Line collision already exists
- tick() updates physics per frame
- renderFrame() handles drawing

---

# ✅ 1. PARTICLE (BALL) MODEL

Ensure all balls have:

```js
ball = {
  x, y,
  vx, vy,
  radius: 4,
  mass: 1
}
```

---

# ✅ 2. PARTICLE ↔ PARTICLE COLLISION

## Detection

```js
function checkCollision(a, b) {
  const dx = b.x - a.x;
  const dy = b.y - a.y;
  const dist = Math.sqrt(dx*dx + dy*dy);

  if (dist < a.radius + b.radius) {
    resolveCollision(a, b, dist, dx, dy);
  }
}
```

---

## Resolution

```js
function resolveCollision(a, b, dist, dx, dy) {
  const nx = dx / (dist || 0.001);
  const ny = dy / (dist || 0.001);

  const dvx = b.vx - a.vx;
  const dvy = b.vy - a.vy;

  const impact = dvx * nx + dvy * ny;

  if (impact > 0) return;

  const impulse = -impact;

  a.vx -= impulse * nx;
  a.vy -= impulse * ny;

  b.vx += impulse * nx;
  b.vy += impulse * ny;

  // Resolve overlap
  const overlap = (a.radius + b.radius) - dist;

  a.x -= nx * overlap * 0.5;
  a.y -= ny * overlap * 0.5;

  b.x += nx * overlap * 0.5;
  b.y += ny * overlap * 0.5;

  // Sound trigger
  triggerCollisionSound(Math.abs(impact));
}
```

---

## Loop

```js
for (let i = 0; i < balls.length; i++) {
  for (let j = i + 1; j < balls.length; j++) {
    checkCollision(balls[i], balls[j]);
  }
}
```

---

## Toggle

```js
state.world.particleCollision = true;
```

---

# ✅ 3. FLIPPER (IMPULSE SURFACE)

## Add Behavior to Line

```js
line.behavior = {
  type: "flipper",

  flipper: {
    pivot: "start",   // "start" or "end"
    direction: 1,     // 1 or -1
    angle: 0,
    maxAngle: 45,
    speed: 10,
    active: false
  }
};
```

---

## Input

```js
if (input.keyPressed) {
  flipper.active = true;
} else {
  flipper.active = false;
}
```

---

## Update

```js
function updateFlipper(line, dt) {
  const f = line.behavior.flipper;

  if (f.active) {
    f.angle += f.speed * f.direction * dt;
  } else {
    f.angle *= 0.8; // return to rest
  }

  f.angle = Math.max(0, Math.min(f.angle, f.maxAngle));
}
```

---

## Apply Rotation

```js
rotateLineAroundPivot(line, f.pivot, f.angle);
```

---

## Collision Boost

On line-ball collision:

```js
if (line.behavior.type === "flipper" && line.behavior.flipper.active) {
  ball.vx *= 1.5;
  ball.vy *= 1.5;

  playSound("flipper-hit");
}
```

---

# 🎛️ 4. UI

## Flipper Toggle

```txt
Behavior:
[ None ▼ | Flipper ]
```

---

## Flipper Controls

```txt
Flipper
[ Left | Right ]
```

Map internally:

```js
if (ui === "left") {
  pivot = "start";
  direction = 1;
}

if (ui === "right") {
  pivot = "end";
  direction = -1;
}
```

---

## Particle Collision Toggle

```txt
[ Particle Collision: ON / OFF ]
```

---

# 🔊 5. SOUND HOOKS

## Collision

```js
function triggerCollisionSound(strength) {
  playSound("collision", strength);
}
```

---

## Flipper

```js
playSound("flipper-hit");
```

---

# 🚀 Implementation Guide

## WHERE

- main.js → tick(), physics loop
- collision system → add ball-ball collision
- line behavior → add flipper
- controls.js → add toggles

---

## RUN

- spawn multiple balls
- enable particle collision
- activate flipper
- verify chain reactions
- verify sound triggers

---

## EXPECT

- balls collide and bounce
- flipper adds impulse
- collisions create rhythmic sound
- system feels interactive and playable

---

# 🎯 END STATE

> A playable interaction system where motion, collision, and user input generate sound dynamically
