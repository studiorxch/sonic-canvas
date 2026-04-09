# 0408_ENGINE_DataModel_v1.0.0
Date: 04-08

---

## Assumptions

- Environment: Browser (Canvas + Web Audio)
- Language: TypeScript
- Architecture: data → logic → render
- Existing engine modules:
  - physics.js
  - swarm.js
  - shapeSystem.js
  - collision.js

---

## Core Architecture

World
 ├── Particles (agents)
 ├── Shapes (visual)
 ├── Mechanics (functional)
 ├── Systems (audio + behavior)

---

## 1. World

```ts
export type WorldMode = 'gravity' | 'flow' | 'zero-g';
export type Direction = 'up' | 'down' | 'left' | 'right';

export interface World {
  mode: WorldMode;
  direction: Direction;
  strength: number;
}
```

---

## 2. Particle System

```ts
export interface ParticleSystem {
  id: string;

  count: number;
  speed: number;
  spread: number;
  size: number;

  instrumentId: string;
  note: string;

  position: Vector2;
  velocity: Vector2;
}
```

---

## 3. Shape

```ts
export type ShapeType =
  | 'circle'
  | 'rect'
  | 'line'
  | 'polygon'
  | 'text';

export interface Shape {
  id: string;
  type: ShapeType;

  position: Vector2;
  rotation: number;
  scale: number;

  strokeWidth: number;
  color: string;

  selectable: boolean;
}
```

---

## 4. Mechanics

```ts
export type MechanicType =
  | 'bumper-hard'
  | 'bumper-elastic'
  | 'ramp'
  | 'field'
  | 'absorber'
  | 'emitter'
  | 'portal';

export interface Mechanic extends Shape {
  mechanicType: MechanicType;

  strength?: number;

  instrumentId?: string;
  note?: string;
  audioMode?: 'musical' | 'ambient' | 'off';

  targetId?: string;
}
```

---

## 5. Instrument

```ts
export type AudioMode = 'musical' | 'ambient' | 'off';

export interface Instrument {
  id: string;
  name: string;

  mode: AudioMode;

  volume: number;
  pitch: number;
  quantize: number;

  sampleMap?: Record<string, string>;
}
```

---

## 6. Behavior

```ts
export type BehaviorType =
  | 'none'
  | 'attract'
  | 'repel'
  | 'turbulence';

export interface Behavior {
  type: BehaviorType;
  strength: number;
}
```

---

## 7. Scene

```ts
export interface Scene {
  id: string;
  name: string;

  world: World;

  particles: ParticleSystem[];
  shapes: Shape[];
  mechanics: Mechanic[];

  instruments: Instrument[];
}
```

---

## 8. Shared Types

```ts
export interface Vector2 {
  x: number;
  y: number;
}
```

---

## Relationships

- Particle → Instrument via instrumentId
- Mechanic → optional Instrument
- Portal → linked via targetId

---

## Minimal Example

```ts
const scene: Scene = {
  id: 'scene-1',
  name: 'Gravity Loop',

  world: {
    mode: 'gravity',
    direction: 'down',
    strength: 0.3,
  },

  particles: [
    {
      id: 'p1',
      count: 10,
      speed: 2,
      spread: 0.5,
      size: 5,
      instrumentId: 'inst1',
      note: 'C1',
      position: { x: 100, y: 100 },
      velocity: { x: 0, y: 0 },
    },
  ],

  shapes: [],

  mechanics: [
    {
      id: 'm1',
      type: 'line',
      mechanicType: 'ramp',
      position: { x: 200, y: 300 },
      rotation: 20,
      scale: 1,
      strokeWidth: 2,
      color: '#fff',
      selectable: true,
    },
  ],

  instruments: [
    {
      id: 'inst1',
      name: 'Mechanical Pack',
      mode: 'musical',
      volume: 1,
      pitch: 0,
      quantize: 1,
    },
  ],
};
```

---

## Implementation Guide

- where: `/engine/types.ts`
- run: import types into physics + swarm systems
- expect: stable linking between physics, audio, UI
